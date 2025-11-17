# Cricbuzz Cricket Match Simulation - Reorganized UML Diagrams

## 1. Complete System - Class Diagram (Organized by Layers)

```mermaid
classDiagram
    %% ========== PRESENTATION LAYER ==========
    class Demo {
        +main(args: String[])$
        -addTeam(name: String): Team
        -addPlayer(name: String, type: PlayerType): PlayerDetails
    }
    
    %% ========== MATCH MANAGEMENT LAYER ==========
    class Match {
        -Team teamA
        -Team teamB
        -Date matchDate
        -String venue
        -Team tossWinner
        -InningDetails[] innings
        -MatchType matchType
        +Match(teamA, teamB, matchDate, venue, matchType)
        +startMatch()
        -toss(teamA, teamB): Team
    }
    
    class MatchType {
        <<interface>>
        +noOfOvers(): int*
        +maxOverCountBowlers(): int*
    }
    
    class T20Match {
        +noOfOvers(): int
        +maxOverCountBowlers(): int
    }
    
    class OneDayMatch {
        +noOfOvers(): int
        +maxOverCountBowlers(): int
    }
    
    %% ========== INNING MANAGEMENT LAYER ==========
    class InningDetails {
        -Team battingTeam
        -Team bowlingTeam
        -MatchType matchType
        -List~OverDetails~ overs
        +InningDetails(battingTeam, bowlingTeam, matchType)
        +start(runsToWin)
        +getTotalRuns(): int
    }
    
    class OverDetails {
        -int overNumber
        -List~BallDetails~ balls
        -int extraBallsCount
        -PlayerDetails bowledBy
        +OverDetails(overNumber, bowledBy)
        +startOver(battingTeam, bowlingTeam, runsToWin): boolean
    }
    
    class BallDetails {
        +int ballNumber
        +BallType ballType
        +RunType runType
        +PlayerDetails playedBy
        +PlayerDetails bowledBy
        +Wicket wicket
        -List~ScoreUpdaterObserver~ scoreUpdaterObserverList
        +BallDetails(ballNumber)
        +startBallDelivery(battingTeam, bowlingTeam, over)
        -notifyUpdaters(ballDetails)
        -getRunType(): RunType
        -isWicketTaken(): boolean
    }
    
    %% ========== TEAM MANAGEMENT LAYER ==========
    class Team {
        +String teamName
        +Queue~PlayerDetails~ playing11
        +List~PlayerDetails~ bench
        +PlayerBattingController battingController
        +PlayerBowlingController bowlingController
        +boolean isWinner
        +Team(teamName, playing11, bench, bowlers)
        +chooseNextBatsMan()
        +chooseNextBowler(maxOverCount)
        +getStriker(): PlayerDetails
        +getCurrentBowler(): PlayerDetails
        +getTotalRuns(): int
        +printBattingScoreCard()
        +printBowlingScoreCard()
    }
    
    class PlayerBattingController {
        -Queue~PlayerDetails~ yetToPlay
        -PlayerDetails striker
        -PlayerDetails nonStriker
        +PlayerBattingController(playing11)
        +getNextPlayer()
        +getStriker(): PlayerDetails
        +setStriker(player)
        +getNonStriker(): PlayerDetails
        +setNonStriker(player)
    }
    
    class PlayerBowlingController {
        -Deque~PlayerDetails~ bowlersList
        -Map~PlayerDetails,Integer~ bowlerVsOverCount
        -PlayerDetails currentBowler
        +PlayerBowlingController(bowlers)
        -setBowlersList(bowlers)
        +getNextBowler(maxOverCount)
        +getCurrentBowler(): PlayerDetails
    }
    
    %% ========== PLAYER LAYER ==========
    class PlayerDetails {
        +Person person
        +PlayerType playerType
        +BattingScoreCard battingScoreCard
        +BowlingScoreCard bowlingScoreCard
        +PlayerDetails(person, playerType)
        +printBattingScoreCard()
        +printBowlingScoreCard()
    }
    
    class Person {
        +String name
        +int age
        +String address
    }
    
    class BattingScoreCard {
        +int totalRuns
        +int totalBallsPlayed
        +int totalFours
        +int totalSix
        +double strikeRate
        +Wicket wicketDetails
    }
    
    class BowlingScoreCard {
        +int totalOversCount
        +int runsGiven
        +int wicketsTaken
        +int noBallCount
        +int wideBallCount
        +double economyRate
    }
    
    %% ========== OBSERVER PATTERN LAYER ==========
    class ScoreUpdaterObserver {
        <<interface>>
        +update(ballDetails: BallDetails)*
    }
    
    class BattingScoreUpdater {
        +update(ballDetails: BallDetails)
    }
    
    class BowlingScoreUpdater {
        +update(ballDetails: BallDetails)
    }
    
    %% ========== DOMAIN MODELS ==========
    class Wicket {
        +WicketType wicketType
        +PlayerDetails takenBy
        +OverDetails overDetail
        +BallDetails ballDetail
        +Wicket(wicketType, takenBy, overDetail, ballDetail)
    }
    
    %% ========== ENUMERATIONS ==========
    class BallType {
        <<enumeration>>
        NORMAL
        WIDEBALL
        NOBALL
    }
    
    class RunType {
        <<enumeration>>
        ZERO
        ONE
        TWO
        THREE
        FOUR
        SIX
    }
    
    class PlayerType {
        <<enumeration>>
        BATSMAN
        BOWLER
        WICKETKEEPER
        CAPTAIN
        ALLROUNDER
    }
    
    class WicketType {
        <<enumeration>>
        RUNOUT
        BOLD
        CATCH
    }
    
    %% ========== RELATIONSHIPS ==========
    %% Presentation to Match
    Demo ..> Match : creates
    Demo ..> Team : creates
    
    %% Match Layer Relationships
    Match o-- Team : has 2
    Match o-- InningDetails : has 2
    Match --> MatchType : uses
    MatchType <|.. T20Match : implements
    MatchType <|.. OneDayMatch : implements
    
    %% Inning Layer Relationships
    InningDetails --> Team : batting/bowling
    InningDetails o-- OverDetails : contains
    InningDetails --> MatchType : uses
    OverDetails o-- BallDetails : has 6
    OverDetails --> PlayerDetails : bowler
    
    %% Ball Relationships
    BallDetails --> PlayerDetails : playedBy/bowledBy
    BallDetails --> BallType : uses
    BallDetails --> RunType : uses
    BallDetails o-- Wicket : may have
    BallDetails o-- ScoreUpdaterObserver : notifies
    
    %% Team Relationships
    Team *-- PlayerBattingController : has
    Team *-- PlayerBowlingController : has
    Team o-- PlayerDetails : has 11
    PlayerBattingController --> PlayerDetails : manages
    PlayerBowlingController --> PlayerDetails : manages
    
    %% Player Relationships
    PlayerDetails *-- Person : has
    PlayerDetails --> PlayerType : is a
    PlayerDetails *-- BattingScoreCard : has
    PlayerDetails *-- BowlingScoreCard : has
    
    %% Score Card Relationships
    BattingScoreCard o-- Wicket : may have
    
    %% Wicket Relationships
    Wicket --> WicketType : uses
    Wicket --> PlayerDetails : taken by
    Wicket --> OverDetails : occurred in
    Wicket --> BallDetails : occurred on
    
    %% Observer Pattern Relationships
    ScoreUpdaterObserver <|.. BattingScoreUpdater : implements
    ScoreUpdaterObserver <|.. BowlingScoreUpdater : implements
    BattingScoreUpdater ..> BattingScoreCard : updates
    BowlingScoreUpdater ..> BowlingScoreCard : updates
```

---

## 2. Sequence Diagram - Complete Match Flow (Fixed)

```mermaid
sequenceDiagram
    participant Demo
    participant Match
    participant TeamA as Team A
    participant TeamB as Team B
    participant Inning as InningDetails
    participant OvrDtl as OverDetails
    participant Ball as BallDetails
    participant Obs as ScoreUpdater
    
    Note over Demo,Obs: Match Initialization
    Demo->>Match: new Match(teamA, teamB, matchType)
    Demo->>Match: startMatch()
    activate Match
    
    Match->>Match: toss()
    Note over Match: Determine batting/bowling teams
    
    rect rgb(200, 220, 240)
        Note over Match,Obs: First Inning
        Match->>Inning: new InningDetails(batting, bowling)
        Match->>Inning: start(runsToWin = 0)
        activate Inning
        
        Inning->>TeamA: chooseNextBatsMan()
        Inning->>TeamA: chooseNextBatsMan()
        Note over TeamA: Striker and Non-Striker selected
        
        loop For each Over (20 or 50)
            Inning->>TeamB: chooseNextBowler(maxOverCount)
            TeamB-->>Inning: bowler
            
            Inning->>OvrDtl: new OverDetails(overNumber, bowler)
            Inning->>OvrDtl: startOver(battingTeam, bowlingTeam, runsToWin)
            activate OvrDtl
            
            loop For 6 balls
                OvrDtl->>Ball: new BallDetails(ballNumber)
                OvrDtl->>Ball: startBallDelivery(batting, bowling, over)
                activate Ball
                
                Ball->>Ball: determine BallType
                Ball->>Ball: isWicketTaken()
                
                alt Wicket Taken
                    Ball->>TeamA: setStriker(null)
                    Note over Ball: create Wicket object
                    OvrDtl->>TeamA: chooseNextBatsMan()
                    alt All players out
                        Ball-->>OvrDtl: inning ends
                    end
                else No Wicket
                    Ball->>Ball: getRunType()
                    alt Odd runs (1 or 3)
                        Ball->>TeamA: swap striker/non-striker
                    end
                end
                
                Ball->>Obs: notifyUpdaters(ballDetails)
                activate Obs
                Obs->>Obs: update BattingScoreCard
                Obs->>Obs: update BowlingScoreCard
                Obs-->>Ball: done
                deactivate Obs
                
                Ball-->>OvrDtl: ball complete
                deactivate Ball
            end
            
            OvrDtl->>TeamA: swap striker and non-striker
            OvrDtl-->>Inning: over complete
            deactivate OvrDtl
        end
        
        Inning-->>Match: inning complete, totalRuns
        deactivate Inning
        
        Match->>TeamA: printBattingScoreCard()
        Match->>TeamB: printBowlingScoreCard()
    end
    
    rect rgb(240, 220, 200)
        Note over Match,Obs: Second Inning
        Match->>Inning: new InningDetails(bowling, batting)
        Match->>Inning: start(runsToWin = firstInningRuns + 1)
        activate Inning
        
        Inning->>TeamB: chooseNextBatsMan()
        Inning->>TeamB: chooseNextBatsMan()
        
        loop For each Over
            Inning->>TeamA: chooseNextBowler(maxOverCount)
            Inning->>OvrDtl: new OverDetails(overNumber, bowler)
            Inning->>OvrDtl: startOver(battingTeam, bowlingTeam, runsToWin)
            activate OvrDtl
            
            loop For 6 balls
                OvrDtl->>Ball: new BallDetails(ballNumber)
                OvrDtl->>Ball: startBallDelivery(batting, bowling, over)
                activate Ball
                
                Ball->>Ball: process ball
                Ball->>Obs: notifyUpdaters(ballDetails)
                
                alt Target Reached
                    Ball-->>OvrDtl: return true (match won)
                    OvrDtl-->>Inning: return true
                end
                
                Ball-->>OvrDtl: ball complete
                deactivate Ball
            end
            
            OvrDtl-->>Inning: over complete
            deactivate OvrDtl
            
            alt Target reached or all out
                Note over Inning: Inning ends
            end
        end
        
        Inning-->>Match: inning complete
        deactivate Inning
        
        Match->>TeamB: printBattingScoreCard()
        Match->>TeamA: printBowlingScoreCard()
    end
    
    Match->>Match: determineWinner()
    Note over Match: Compare runs, set isWinner flag
    Match-->>Demo: match complete
    deactivate Match
```

---

## 3. Observer Pattern - Score Update Flow

```mermaid
sequenceDiagram
    participant Ball as BallDetails<br/>(Subject)
    participant BatObs as BattingScoreUpdater<br/>(Observer)
    participant BowlObs as BowlingScoreUpdater<br/>(Observer)
    participant BatCard as BattingScoreCard
    participant BowlCard as BowlingScoreCard
    
    Note over Ball: Ball delivery completed
    Ball->>Ball: notifyUpdaters(this)
    
    rect rgb(220, 240, 220)
        Note over Ball,BatCard: Update Batting Score
        Ball->>BatObs: update(ballDetails)
        activate BatObs
        
        BatObs->>BatObs: Extract run type
        
        alt RunType is ONE, TWO, THREE, FOUR, or SIX
            BatObs->>BatCard: totalRuns += runValue
        end
        
        BatObs->>BatCard: totalBallsPlayed++
        
        alt RunType is FOUR
            BatObs->>BatCard: totalFours++
        else RunType is SIX
            BatObs->>BatCard: totalSix++
        end
        
        alt Wicket taken
            BatObs->>BatCard: wicketDetails = wicket
        end
        
        BatObs->>BatCard: calculate strikeRate
        Note over BatCard: strikeRate = (runs/balls) * 100
        
        BatObs-->>Ball: update complete
        deactivate BatObs
    end
    
    rect rgb(240, 220, 220)
        Note over Ball,BowlCard: Update Bowling Score
        Ball->>BowlObs: update(ballDetails)
        activate BowlObs
        
        alt Ball number is 6 AND BallType is NORMAL
            BowlObs->>BowlCard: totalOversCount++
        end
        
        alt RunType is ONE, TWO, THREE, FOUR, or SIX
            BowlObs->>BowlCard: runsGiven += runValue
        end
        
        alt Wicket taken
            BowlObs->>BowlCard: wicketsTaken++
        end
        
        alt BallType is NOBALL
            BowlObs->>BowlCard: noBallCount++
        else BallType is WIDEBALL
            BowlObs->>BowlCard: wideBallCount++
        end
        
        BowlObs->>BowlCard: calculate economyRate
        Note over BowlCard: economy = runs / (overs + balls/6)
        
        BowlObs-->>Ball: update complete
        deactivate BowlObs
    end
```

---

## 4. Strategy Pattern - Match Type

```mermaid
classDiagram
    class Match {
        -MatchType matchType
        +startMatch()
        +getMatchOvers()
        +getMaxBowlerOvers()
    }
    
    class MatchType {
        <<Strategy Interface>>
        +noOfOvers(): int*
        +maxOverCountBowlers(): int*
    }
    
    class T20Match {
        <<Concrete Strategy>>
        +noOfOvers(): int
        +maxOverCountBowlers(): int
    }
    
    class OneDayMatch {
        <<Concrete Strategy>>
        +noOfOvers(): int
        +maxOverCountBowlers(): int
    }
    
    class TestMatch {
        <<Potential Strategy>>
        +noOfOvers(): int
        +maxOverCountBowlers(): int
    }
    
    Match --> MatchType : uses
    MatchType <|.. T20Match : implements
    MatchType <|.. OneDayMatch : implements
    MatchType <|.. TestMatch : implements
    
    note for T20Match "Returns:\n- 20 overs\n- Max 5 overs/bowler"
    note for OneDayMatch "Returns:\n- 50 overs\n- Max 10 overs/bowler"
    note for TestMatch "Returns:\n- Unlimited overs\n- No bowler limit"
```

---

## 5. System Architecture - Layered View

```mermaid
graph TB
    subgraph "Layer 1: Presentation"
        Demo[Demo/Main Class]
    end
    
    subgraph "Layer 2: Match Orchestration"
        Match[Match Controller]
        MatchType[Match Type Strategy]
    end
    
    subgraph "Layer 3: Inning Management"
        Inning[Inning Details]
        Over[Over Details]
        Ball[Ball Details]
    end
    
    subgraph "Layer 4: Team Management"
        Team[Team]
        BatCtrl[Batting Controller]
        BowlCtrl[Bowling Controller]
    end
    
    subgraph "Layer 5: Player Management"
        Player[Player Details]
        Person[Person Info]
        PlayerType[Player Type Enum]
    end
    
    subgraph "Layer 6: Score Management"
        Observer[Score Observer Interface]
        BatUpdate[Batting Score Updater]
        BowlUpdate[Bowling Score Updater]
        BatScore[Batting Score Card]
        BowlScore[Bowling Score Card]
    end
    
    subgraph "Layer 7: Domain Models"
        Wicket[Wicket]
        BallType[Ball Type Enum]
        RunType[Run Type Enum]
        WicketType[Wicket Type Enum]
    end
    
    Demo --> Match
    Match --> MatchType
    Match --> Inning
    Match --> Team
    
    Inning --> Over
    Over --> Ball
    
    Team --> BatCtrl
    Team --> BowlCtrl
    Team --> Player
    
    Player --> Person
    Player --> PlayerType
    Player --> BatScore
    Player --> BowlScore
    
    Ball --> Observer
    Observer --> BatUpdate
    Observer --> BowlUpdate
    BatUpdate --> BatScore
    BowlUpdate --> BowlScore
    
    Ball --> Wicket
    Ball --> BallType
    Ball --> RunType
    Wicket --> WicketType
    
    style Demo fill:#e1f5ff,stroke:#333,stroke-width:2px
    style Match fill:#fff4e1,stroke:#333,stroke-width:2px
    style Observer fill:#ffe1f5,stroke:#333,stroke-width:2px
    style Team fill:#e8f5e9,stroke:#333,stroke-width:2px
    style Ball fill:#ffe4e1,stroke:#333,stroke-width:2px
```

---

## 6. Ball Delivery State Machine

```mermaid
stateDiagram-v2
    [*] --> BallCreated: new BallDetails()
    BallCreated --> PlayersSet: Set playedBy & bowledBy
    PlayersSet --> DetermineBallType: Assign ball type
    
    DetermineBallType --> CheckWicket: Ball delivered
    
    state CheckWicket {
        [*] --> WicketCheck
        WicketCheck --> Wicket: 20% probability
        WicketCheck --> NoWicket: 80% probability
    }
    
    state Wicket {
        [*] --> SetRunZero
        SetRunZero --> CreateWicketObject
        CreateWicketObject --> ClearStriker
        ClearStriker --> [*]
    }
    
    state NoWicket {
        [*] --> GenerateRunType
        GenerateRunType --> CheckOddRuns
        CheckOddRuns --> SwapBatsmen: If 1 or 3
        CheckOddRuns --> KeepBatsmen: If 0, 2, 4, 6
        SwapBatsmen --> [*]
        KeepBatsmen --> [*]
    }
    
    CheckWicket --> NotifyObservers
    
    state NotifyObservers {
        [*] --> BattingUpdate
        [*] --> BowlingUpdate
        BattingUpdate --> [*]
        BowlingUpdate --> [*]
    }
    
    NotifyObservers --> BallComplete
    BallComplete --> [*]
```

---

## 7. Over Completion Flow

```mermaid
flowchart TD
    Start([Over Starts]) --> CreateOver[Create OverDetails<br/>overNumber, bowler]
    CreateOver --> BallLoop{Ball count < 6?}
    
    BallLoop -->|Yes| CreateBall[Create BallDetails]
    CreateBall --> DeliverBall[startBallDelivery]
    
    DeliverBall --> ProcessBall[Process ball result]
    ProcessBall --> CheckWicket{Wicket<br/>taken?}
    
    CheckWicket -->|Yes| NextBatsman[Choose next batsman]
    NextBatsman --> CheckAllOut{All players<br/>out?}
    CheckAllOut -->|Yes| InningEnd([Inning Ends])
    CheckAllOut -->|No| CheckTarget
    
    CheckWicket -->|No| CheckTarget{Target<br/>reached?}
    CheckTarget -->|Yes 2nd Inning| MatchWon([Team Wins])
    CheckTarget -->|No| IncrementBall[ballCount++]
    
    IncrementBall --> BallLoop
    
    BallLoop -->|No 6 balls done| SwapEnds[Swap striker & non-striker]
    SwapEnds --> OverComplete([Over Complete])
    
    style Start fill:#4CAF50,color:#fff
    style InningEnd fill:#F44336,color:#fff
    style MatchWon fill:#FF9800,color:#fff
    style OverComplete fill:#2196F3,color:#fff
```

---

## 8. Team Structure Diagram

```mermaid
graph TB
    subgraph Team["Team Structure"]
        direction TB
        TeamRoot[Team<br/>teamName: String<br/>isWinner: boolean]
        
        subgraph Players["Players Collection"]
            Playing11[Playing 11<br/>Queue of PlayerDetails]
            Bench[Bench Players<br/>List of PlayerDetails]
        end
        
        subgraph Controllers["Team Controllers"]
            BatCtrl[PlayerBattingController]
            BowlCtrl[PlayerBowlingController]
        end
        
        TeamRoot --> Players
        TeamRoot --> Controllers
    end
    
    subgraph BattingController["Batting Controller Details"]
        YetToPlay[yetToPlay: Queue]
        Striker[striker: PlayerDetails]
        NonStriker[nonStriker: PlayerDetails]
        
        GetNext[getNextPlayer]
        SetStriker[setStriker]
        GetStriker[getStriker]
    end
    
    subgraph BowlingController["Bowling Controller Details"]
        BowlersList[bowlersList: Deque]
        BowlerCount[bowlerVsOverCount: Map]
        CurrentBowler[currentBowler: PlayerDetails]
        
        GetNextBowler[getNextBowler]
        GetCurrent[getCurrentBowler]
    end
    
    subgraph PlayerStructure["Player Details"]
        PersonInfo[Person<br/>name, age, address]
        PType[PlayerType<br/>ALLROUNDER, BATSMAN, etc]
        BatScore[BattingScoreCard]
        BowlScore[BowlingScoreCard]
    end
    
    BatCtrl -.-> BattingController
    BowlCtrl -.-> BowlingController
    Playing11 -.-> PlayerStructure
    
    style TeamRoot fill:#4CAF50,color:#fff
    style BatCtrl fill:#2196F3,color:#fff
    style BowlCtrl fill:#FF9800,color:#fff
```

---

## 9. Match State Flow Diagram

```mermaid
stateDiagram-v2
    [*] --> MatchCreated: Demo creates Match
    MatchCreated --> TossCompleted: Conduct toss
    
    TossCompleted --> Inning1: Start First Inning
    
    state Inning1 {
        [*] --> SelectBatsmen: Choose 2 batsmen
        SelectBatsmen --> OverLoop
        
        state OverLoop {
            [*] --> SelectBowler
            SelectBowler --> DeliverBalls
            
            state DeliverBalls {
                [*] --> Ball1
                Ball1 --> Ball2
                Ball2 --> Ball3
                Ball3 --> Ball4
                Ball4 --> Ball5
                Ball5 --> Ball6
                
                note right of Ball1
                    After each ball:
                    - Check wicket
                    - Update scores
                    - Swap batsmen if needed
                end note
            }
            
            DeliverBalls --> [*]: 6 balls done
        }
        
        OverLoop --> OverLoop: Next over
        OverLoop --> [*]: All overs done OR all out
    }
    
    Inning1 --> PrintScores1: Print scorecards
    PrintScores1 --> Inning2: Start Second Inning
    
    state Inning2 {
        [*] --> SetTarget: Target = firstInningRuns + 1
        SetTarget --> SelectBatsmen2
        SelectBatsmen2 --> ChaseLoop
        
        state ChaseLoop {
            [*] --> SelectBowler2
            SelectBowler2 --> DeliverBalls2
            
            DeliverBalls2 --> CheckTarget: After each ball
            CheckTarget --> TargetReached: If runs >= target
            CheckTarget --> Continue: Otherwise
            Continue --> DeliverBalls2
            
            DeliverBalls2 --> [*]: Over complete
        }
        
        ChaseLoop --> ChaseLoop: Next over
        ChaseLoop --> [*]: Target reached OR overs done
    }
    
    Inning2 --> PrintScores2: Print scorecards
    PrintScores2 --> DetermineWinner: Compare total runs
    
    state DetermineWinner {
        [*] --> CompareScores
        CompareScores --> SetWinner: Mark winner team
        SetWinner --> [*]
    }
    
    DetermineWinner --> MatchComplete
    MatchComplete --> [*]
    
    note right of Inning1
        Batting team tries to
        score maximum runs
    end note
    
    note right of Inning2
        Chasing team needs to
        score target runs
    end note
```

---

## 10. Observer Pattern - Detailed Class Structure

```mermaid
classDiagram
    class BallDetails {
        <<Subject>>
        -List~ScoreUpdaterObserver~ scoreUpdaterObserverList
        +notifyUpdaters(ballDetails)
        +startBallDelivery()
    }
    
    class ScoreUpdaterObserver {
        <<Observer Interface>>
        +update(ballDetails: BallDetails)*
    }
    
    class BattingScoreUpdater {
        <<Concrete Observer>>
        +update(ballDetails: BallDetails)
        -updateRuns(runType, scoreCard)
        -updateBalls(scoreCard)
        -updateBoundaries(runType, scoreCard)
        -updateWicket(wicket, scoreCard)
    }
    
    class BowlingScoreUpdater {
        <<Concrete Observer>>
        +update(ballDetails: BallDetails)
        -updateOvers(ballNumber, ballType, scoreCard)
        -updateRunsGiven(runType, scoreCard)
        -updateWickets(wicket, scoreCard)
        -updateExtras(ballType, scoreCard)
    }
    
    class BattingScoreCard {
        +int totalRuns
        +int totalBallsPlayed
        +int totalFours
        +int totalSix
        +double strikeRate
        +Wicket wicketDetails
    }
    
    class BowlingScoreCard {
        +int totalOversCount
        +int runsGiven
        +int wicketsTaken
        +int noBallCount
        +int wideBallCount
        +double economyRate
    }
    
    BallDetails o-- ScoreUpdaterObserver : maintains list
    ScoreUpdaterObserver <|.. BattingScoreUpdater : implements
    ScoreUpdaterObserver <|.. BowlingScoreUpdater : implements
    BattingScoreUpdater ..> BattingScoreCard : updates
    BowlingScoreUpdater ..> BowlingScoreCard : updates
    
    note for BallDetails "After ball delivery:\n1. Determine outcome\n2. Notify all observers\n3. Observers update scores"
    note for BattingScoreUpdater "Updates:\n- Runs scored\n- Balls played\n- Fours and sixes\n- Wicket details"
    note for BowlingScoreUpdater "Updates:\n- Overs bowled\n- Runs conceded\n- Wickets taken\n- Extras (wides, no-balls)"
```

---

## 11. Design Patterns Summary

```mermaid
mindmap
    root((Design Patterns<br/>in Cricbuzz))
        Observer Pattern
            Purpose: Score Updates
            Subject: BallDetails
            Observers
                BattingScoreUpdater
                BowlingScoreUpdater
            Benefits
                Loose coupling
                Easy to add observers
                Automatic notifications
        Strategy Pattern
            Purpose: Match Rules
            Context: Match
            Strategies
                T20Match
                OneDayMatch
                TestMatch
            Benefits
                Runtime strategy change
                Easy to add formats
                Encapsulated algorithms
        Controller Pattern
            Purpose: Separate Logic
            Controllers
                PlayerBattingController
                PlayerBowlingController
            Benefits
                Single Responsibility
                Testable
                Reusable
        Facade Pattern
            Purpose: Simplified Interface
            Facade: Team
            Subsystems
                BattingController
                BowlingController
                PlayerDetails
            Benefits
                Hide complexity
                Unified interface
```

---
