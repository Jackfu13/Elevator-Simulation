# Elevator Simulation (Java, FSM-driven)

This project simulates elevator behavior using a finite state machine (FSM) and a per-tick scheduler. It includes a JavaFX GUI, CSV-driven passenger arrivals, and configurable elevator parameters.

## Goals
- Model realistic elevator behavior with clear, testable state transitions
- Explore scheduling choices (direction, prioritization, capacity, patience)
- Produce logs and per-passenger metrics for analysis

## Features
- FSM-driven elevator states: STOP, MVTOFLR, OPENDR, OFFLD, BOARD, CLOSEDR, MV1FLR
- Call prioritization across floors via `CallManager`
- Passenger groups with politeness and give-up (timeout) behavior
- CSV-based configuration and test scenarios
- Logging and per-passenger outcome exports

## Project structure
- `src/building/`: core simulation (Building, Elevator, Floor, Passengers, CallManager)
- `src/`: GUI (`ElevatorSimulation`) and controller (`ElevatorSimController`)
- `src/genericqueue/`: queue utility
- `src/myfileio/`: lightweight file I/O helpers
- `test_data/`, `test_data2/`: sample passenger scenarios and configs
- `cmpElevator.jar`: optional log comparison tool used by some tests

## Quick start (GUI)
1. Use a JDK with JavaFX (or add the JavaFX SDK to your IDE run configuration).
2. Place a config file named `ElevatorSimConfig.csv` in the repo root. For example:
   - copy `test_data2/ElevatorSimConfig.csv` to `ElevatorSimConfig.csv`
3. Ensure `passCSV` in that file points to a valid passenger CSV (e.g. `test_data2/ElevatorTest.csv`).
4. Run `ElevatorSimulation` (JavaFX `Application`) with the working directory set to the repo root.

## Run (CLI)
From the repo root (example for macOS/Linux):
```sh
export PATH_TO_FX=/path/to/javafx-sdk/lib
javac --module-path "$PATH_TO_FX" --add-modules javafx.controls,javafx.graphics -d out $(find src -name "*.java")
java --module-path "$PATH_TO_FX" --add-modules javafx.controls,javafx.graphics -cp out ElevatorSimulation
```
On Windows, use `;` instead of `:` in module paths and an equivalent file list (e.g. `dir /s /b src\\*.java`).

## Configuration
`ElevatorSimConfig.csv` is read from the working directory by `ElevatorSimController` and supports:
- `numFloors`
- `numElevators`
- `passCSV` (path to passenger CSV)
- `capacity`
- `floorTicks`
- `doorTicks`
- `passPerTick`

### Configuration example
`ElevatorSimConfig.csv`:
```csv
numFloors,6
numElevators,1
passCSV,test_data2/ElevatorTest.csv
capacity,15
floorTicks,5
doorTicks,2
passPerTick,3
```

## Passenger CSV format
Header + rows:
`Time,NumPass,FromFloor,To Floor,Polite,Wait`

Floors in the CSV are 1-based; the simulation converts them to 0-based internally.

Example:
```csv
Time,NumPass,FromFloor,To Floor,Polite,Wait
10,3,1,6,TRUE,1000
```

## Outputs
- `<passCSV-name>.log` if logging is enabled (GUI "Log" button or `Building.enableLogging()` in tests)
- `<passCSV-name>PassData.csv` with per-passenger outcomes (wait-to-board, total time, give-up)

Example log lines:
```
CONFIG:   Capacity=15   Ticks-Floor=5   Ticks-Door=2   Ticks-Passengers=3   CurrState=STOP   CurrFloor=1
Time=12   Prev State: STOP     Curr State: MVTOFLR   PrevFloor: 1   CurrFloor: 1
```

Example PassData rows:
```csv
ID,Number,From,To,WaitToBoard,TotalTime
0,3,1,6,5,23
1,2,3,1,12,-1
```

## Assumptions and limitations
- The GUI currently visualizes a single elevator, even if multiple are configured.
- `ElevatorSimConfig.csv` is loaded from the working directory, so run from the repo root.
- Passenger CSVs use 1-based floors; internal simulation uses 0-based indexing.
- JavaFX is required to run the GUI.

## Screenshots
Add a GIF or screenshot of the GUI here to make the README more inviting.

## Tests
JUnit 5 test suites live in `src/` (e.g. `BuildingFSMBasicTest`, `ExecFullElevatorTests`). Run them from your IDE or a JUnit runner with the working directory set to the repo root. Some tests invoke `cmpElevator.jar` for log comparisons.
