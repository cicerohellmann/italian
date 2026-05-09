# Adaptive Italian Practice Engine UML

This UML document describes the planned adaptive practice engine as a separate site/module that consumes Italian curriculum data, generates exercises, tracks active-recall mastery, and unlocks harder content over time.

## System Context

```mermaid
flowchart LR
  Learner[Italian Learner] --> PracticeSite[Adaptive Practice Site]
  PracticeSite -. optional future link .-> StudyGuide[Existing Study Guide<br/>index.html]
  PracticeSite --> LocalStorage[(Browser localStorage)]
  PracticeSite --> ImageManifest[Curated Image Manifest]
  ImageImporter[Future Image Importer] --> ImageManifest
  Openverse[Openverse] -. future import .-> ImageImporter
  Wikimedia[Wikimedia Commons] -. fallback import .-> ImageImporter
```

## Component Diagram

```mermaid
flowchart TB
  subgraph UI[UI Layer]
    Sidebar[Module Sidebar]
    PracticePanel[Practice Panel]
    ConceptPanel[Concept Detail Panel]
    ReviewPanel[Review / Results Panel]
  end

  subgraph Engine[Practice Engine]
    Generator[Exercise Generator]
    Scorer[Scoring Engine]
    Unlocker[Unlock Engine]
    Scheduler[Review Scheduler]
    Aggregator[Package Mastery Aggregator]
  end

  subgraph Data[Curriculum Data]
    Modules[Modules]
    Concepts[Concepts]
    Vocabulary[Vocabulary Items]
    Verbs[Verb Forms]
    Templates[Phrase Templates]
    Images[Image Manifest]
  end

  subgraph State[User State]
    Progress[Concept Progress]
    PhraseProgress[Phrase Progress]
    Exposure[Context Exposure]
    Preferences[Practice Preferences]
  end

  Sidebar --> Aggregator
  Sidebar --> Unlocker
  PracticePanel --> Generator
  PracticePanel --> Scorer
  ConceptPanel --> Progress
  ReviewPanel --> Progress

  Generator --> Modules
  Generator --> Concepts
  Generator --> Vocabulary
  Generator --> Verbs
  Generator --> Templates
  Generator --> Images
  Generator --> Progress
  Generator --> Unlocker
  Generator --> Scheduler

  Scorer --> Progress
  Scorer --> PhraseProgress
  Scorer --> Exposure
  Unlocker --> Progress
  Unlocker --> Modules
  Unlocker --> Templates
  Scheduler --> Progress
  Aggregator --> Progress
  Aggregator --> Modules
```

## Class Diagram

```mermaid
classDiagram
  class Module {
    +string id
    +string title
    +number level
    +string[] conceptIds
    +UnlockRule unlock
  }

  class Concept {
    +string id
    +ConceptType type
    +string label
    +string meaning
    +string[] moduleIds
    +number difficulty
  }

  class VocabularyItem {
    +string id
    +string italian
    +string english
    +string semanticType
    +string gender
    +string number
    +string article
    +object prepositions
    +string[] imageIds
  }

  class VerbEntry {
    +string id
    +string infinitive
    +string english
    +string auxiliary
    +bool reflexive
    +VerbForm[] forms
  }

  class VerbForm {
    +string id
    +string verbId
    +string tense
    +string mood
    +string person
    +string form
  }

  class PhraseTemplate {
    +string id
    +string template
    +string[] moduleIds
    +TemplateSlot[] slots
    +string[] contextConceptIds
    +string[] unlockRequires
    +number difficulty
  }

  class TemplateSlot {
    +string key
    +string answer
    +string[] conceptIds
    +string[] acceptedAnswers
    +SlotConstraint constraints
  }

  class Exercise {
    +string id
    +ExerciseType type
    +string moduleId
    +Prompt prompt
    +AnswerTarget[] targets
    +string[] contextConceptIds
  }

  class AnswerTarget {
    +string slotKey
    +string expected
    +string[] accepted
    +string[] conceptIds
  }

  class ConceptProgress {
    +string conceptId
    +number attempts
    +number correct
    +number wrong
    +number streak
    +number mastery
    +object confusions
    +number seenInContext
    +Date lastPracticed
  }

  class PhraseProgress {
    +string templateId
    +number attempts
    +number fullCorrect
    +number mastery
    +Date lastPracticed
  }

  class ImageAsset {
    +string id
    +string conceptId
    +string url
    +string thumbUrl
    +string source
    +string creator
    +string license
    +string attributionUrl
  }

  Module "1" --> "*" Concept
  Concept "1" --> "0..1" VocabularyItem
  Concept "1" --> "0..*" VerbForm
  VerbEntry "1" --> "*" VerbForm
  PhraseTemplate "1" --> "*" TemplateSlot
  PhraseTemplate "*" --> "*" Concept
  Exercise "1" --> "*" AnswerTarget
  AnswerTarget "*" --> "*" Concept
  ConceptProgress "1" --> "1" Concept
  PhraseProgress "1" --> "1" PhraseTemplate
  VocabularyItem "1" --> "*" ImageAsset
```

## Exercise Generation Sequence

```mermaid
sequenceDiagram
  participant U as User
  participant UI as Practice UI
  participant G as Exercise Generator
  participant UN as Unlock Engine
  participant ST as Progress State
  participant CD as Curriculum Data

  U->>UI: choose package or global practice
  UI->>G: request next exercise
  G->>UN: ask for unlocked modules/templates
  UN->>ST: read mastery and attempts
  UN->>CD: read unlock requirements
  UN-->>G: return eligible content
  G->>ST: read weak/due concepts
  G->>CD: select compatible template and vocabulary
  G-->>UI: return generated exercise
  UI-->>U: render prompt, blanks, images, choices
```

## Scoring Sequence

```mermaid
sequenceDiagram
  participant U as User
  participant UI as Practice UI
  participant SC as Scoring Engine
  participant CP as Concept Progress State
  participant PP as Phrase Progress State
  participant AG as Package Aggregator

  U->>UI: submit answer
  UI->>SC: score exercise result
  SC->>SC: compare active answer targets only
  SC->>CP: update direct mastery for correct/wrong targets
  SC->>CP: record confusions for wrong targets
  SC->>CP: increment seenInContext for visible context only
  SC->>PP: update phrase mastery only if all targets correct
  SC->>AG: recompute package mastery
  AG-->>UI: package colors and concept colors
  PP-->>UI: phrase pattern status
  UI-->>U: show feedback and next action
```

## Unlock State Machine

```mermaid
stateDiagram-v2
  [*] --> Locked
  Locked --> Available: all required concepts >= learning
  Available --> Learning: active recall correct at least once
  Learning --> Comfortable: stable correct recall
  Comfortable --> Mastered: high mastery + streak
  Mastered --> Comfortable: repeated mistakes
  Comfortable --> Learning: sustained mistakes
  Learning --> Available: long inactivity or repeated failures

  note right of Locked
    Phrase exercises remain locked until
    every required concept reaches
    the selected readiness threshold.
  end note

  note right of Mastered
    Mastery is active-recall only.
    Passive exposure never upgrades state.
  end note
```

## Package Mastery Aggregation

```mermaid
flowchart LR
  C1[Concept mastery<br/>articles] --> P[Package mastery]
  C2[Concept mastery<br/>possessives] --> P
  C3[Concept mastery<br/>prepositions] --> P
  C4[Concept mastery<br/>verbs] --> P
  E1[seenInContext] -. metadata only .-> UI[Exposure badge]
  E1 -. no effect .-> P
  P --> Color[Sidebar color marker]
```

## Phrase Exercise Scoring Example

```mermaid
flowchart TB
  Prompt["Sono ____. Mi occupo soprattutto ____ app Android. E tu, che ____ ____?"]
  A1["sviluppatore software<br/>correct"]
  A2["di expected<br/>user wrote in"]
  A3["lavoro<br/>correct"]
  A4["fai<br/>correct"]
  Context["Sono / Mi occupo soprattutto / app Android / E tu / che<br/>visible context"]

  Prompt --> A1 --> M1[Improve vocabulary mastery]
  Prompt --> A2 --> M2[Record wrong + confusion: in]
  Prompt --> A3 --> M3[Improve vocabulary mastery]
  Prompt --> A4 --> M4[Improve fare/fai mastery]
  Prompt --> Context --> M5[seenInContext only]
  A2 --> Phrase[Phrase not mastered]
```

## Deployment / Runtime View

```mermaid
flowchart TB
  Browser[Browser]
  subgraph StaticFiles[Static Project Files]
    Index[index.html<br/>current guide]
    Mastery[mastery.html<br/>planned practice engine]
    Tracker[adaptive_practice_engine_files.md]
    UML[adaptive_practice_engine_uml.md]
  end

  Browser --> Index
  Browser --> Mastery
  Mastery --> LS[(localStorage)]
  Mastery --> Manifest[Image manifest data]
```

## Implementation Boundary

The first slice must match the tracker: static shell, curriculum data, localStorage state, and basic sidebar only. Exercises and phrase unlocks are later first-version capabilities, not first-slice work.

```mermaid
flowchart LR
  A[First slice] --> B[Static mastery.html]
  A --> C[Hardcoded curriculum data]
  A --> D[localStorage progress shell]
  A --> E[Basic sidebar + default markers]

  L[Later first-version capability] --> M[Vocabulary + module exercises]
  L --> N[Phrase templates + unlocks]
  L --> O[Dynamic known-vocabulary generation]

  X[Not first version] --> Y[Server persistence]
  X --> Z[AI free-form generation]
  X --> I[Live image search during practice]
  X --> J[GF / SimpleNLG integration]
```
