# Logistics on Rails — Railway Freight Management System

An integrated **railway freight logistics, warehouse management, and network analysis system** developed as a **3rd-semester Integrative Project** at **ISEP — Instituto Superior de Engenharia do Porto** (2025/2026). The project brings together Java application development, Oracle database programming, graph algorithms, spatial data structures, and embedded C/RISC-V Assembly.

## Overview

**Logistics on Rails** is an academic proof of concept for managing freight operations across a railway network. It addresses connected logistics challenges: receiving cargo into warehouses, allocating inventory to orders, planning train movements, analysing railway infrastructure, and managing station operations.

The repository combines several areas of computer engineering:

- **Java** — Console-based application, domain logic, graph algorithms, warehouse workflows, and geographical queries.
- **Oracle SQL / PL/SQL** — Railway relational model, data population, queries, and database-side operations.
- **C and RISC-V Assembly** — Railway station management, sensor-related utilities, track-state handling, and low-level routines.

The system is organised around an iterative, three-sprint academic project. Some modules depend on the local educational Oracle environment or a RISC-V toolchain.

## Features

### Warehouse & Cargo Handling

- Import and process warehouse, item, wagon, order, and return records from CSV datasets.
- Store and dispatch stock using **FEFO/FIFO** ordering rules.
- Evaluate order eligibility and allocate inventory by product, box, and location.
- Build trolley picking plans subject to weight constraints, using **First Fit**, **First Fit Decreasing**, and **Best Fit Decreasing** heuristics.
- Sequence warehouse picking routes using deterministic and nearest-neighbour approaches.
- Handle customer returns through quarantine, inspection, restocking or disposal, with audit logging.

### Freight Planning & Railway Operations

- Estimate journey times between stations using railway and locomotive parameters.
- Plan freight routes, assemble trains, and work with a console-based freight dispatcher.
- Model railway entities including stations, lines, segments, routes, locomotives, wagons, and freight.
- Use Oracle-backed railway data and PL/SQL operations in applicable workflows.

### Railway Network Analysis

- **Graph representations:** adjacency-map and adjacency-matrix graph implementations.
- **Graph traversal and shortest paths:** BFS, DFS, Dijkstra, and risk-aware route analysis with negative-cost handling.
- **Infrastructure upgrade planning:** dependency ordering and cycle-related analysis.
- **Minimal backbone:** minimum spanning network analysis and Graphviz DOT/SVG output.
- **Hub analytics:** station centrality metrics, including degree/strength, betweenness, and harmonic closeness.
- **Maximum flow:** Edmonds–Karp-style residual-network analysis for corridor capacity.

### Geographic Station Queries

- Load a large European railway station dataset (approximately **64,000 records**).
- Index station attributes using **binary search trees and AVL trees**.
- Construct a balanced **2D spatial tree (KD-tree)** for latitude/longitude queries.
- Search stations by geographic area, proximity, and radius, including filtered lookups.
- Use geographic distance calculations for nearest-station and radius searches.

### Embedded Railway Station Management

- RISC-V Assembly routines for data encryption/decryption, sensor-data parsing, command formatting, circular-buffer operations, sorting, and median calculations.
- C modules for station setup, user interaction, operational data, track management, and logging.
- An Arduino sketch for sensor and LED-related hardware functions.
- RISC-V executable compilation and QEMU-based emulation through the provided `stationMngt/Makefile`.

## Tech Stack

| Category | Technology |
| --- | --- |
| Application | Java 17, Maven |
| Database | Oracle Database, SQL, PL/SQL, JDBC (`ojdbc8`) |
| Data structures | BST, AVL tree, KD-tree, custom graph implementations |
| Algorithms | Graph traversal, shortest paths, minimum spanning trees, max flow, spatial search, packing heuristics |
| Embedded | C, RISC-V Assembly, Arduino |
| Emulation / Build | QEMU, GNU Make, RISC-V cross-compiler |
| Testing | JUnit 4, Maven Surefire, JaCoCo |
| Data import | CSV, Apache POI for Excel files |
| Visualisation | Graphviz DOT/SVG exports |
| Workflow | Git, GitHub, Scrum documentation |

## Architecture

The Java application uses a **layered, console-oriented design**, with domain models and reusable data structures shared across its operations.

```text
                   Console User Interface
                            |
                       Controllers
                            |
                   Services / Algorithms
                            |
               Domain Models & Repositories
                     /              \
              CSV datasets       Oracle JDBC

             Independent station-management module
               C / RISC-V Assembly / Arduino
```

- `Bootstrap` loads available CSV datasets into the application's repositories when the application starts.
- Domain classes represent logistics and railway entities.
- Controllers and services coordinate warehouse, spatial-query, and network-analysis operations.
- Custom tree and graph structures support algorithmic and geographical processing.
- SQL/PLSQL scripts and the embedded station-management source are organised as separate project modules.

## Project Structure

```text
├── src/
│   ├── main/java/pt/ipp/isep/dei/pi25_26/g093/app/
│   │   ├── Main.java                # Java entry point
│   │   ├── Bootstrap.java           # Dataset initialisation
│   │   ├── controller/              # Application controllers
│   │   ├── datastructures/          # BST and AVL implementations
│   │   ├── domain/                  # Railway and warehouse entities
│   │   ├── graphs/                  # Graph types and algorithms
│   │   ├── repositories/            # In-memory repositories
│   │   ├── service/                 # Business and algorithm services
│   │   ├── ui/                      # Console menus, including LP workflows
│   │   └── utilities/               # CSV, Excel and database utilities
│   └── test/java/                   # JUnit tests
├── sql_scripts/                     # Oracle SQL and PL/SQL, by user story
├── stationMngt/                     # Station-management C / RISC-V / Arduino
│   ├── assembly/
│   ├── arduino/
│   ├── data/
│   ├── general/
│   ├── interface/
│   ├── manager/
│   └── Makefile
├── train_station_dataset/           # Railway and warehouse CSV datasets
├── generated-graphs/               # Graphviz DOT and SVG outputs
├── doc/                            # Requirements, diagrams and analyses
├── SCRUM/                          # Sprint planning and burndown files
├── logs/                           # Operational and audit logs
└── pom.xml                         # Maven configuration
```

## How to Run

### Prerequisites

- **JDK 17** or later compatible with the Maven Java 17 configuration.
- **Apache Maven 3.x**.
- Run commands from the **repository root**, since the bootstrap loads data using the relative `train_station_dataset/` path.
- For database-dependent functionalities: access to an Oracle database and valid JDBC credentials.
- For the embedded module: compatible **RISC-V 32-bit cross-compiler**, **QEMU user-mode emulator**, and GNU Make.

### Build & Test (Java)

```bash
mvn clean compile
mvn test
mvn package
```

### Run the Java Application

```bash
mvn exec:java
```

The Maven `exec-maven-plugin` is configured to run:

```text
pt.ipp.isep.dei.pi25_26.g093.app.Main
```

At startup, `Bootstrap` requests CSV filenames relative to `train_station_dataset/`. Example dataset filenames include `bays.csv`, `items.csv`, `orders.csv`, `order_lines.csv`, `returns.csv`, `wagons.csv`, `train_stations_europe.csv`, `lines.csv`, and `stations.csv`.

> **Database configuration:** The supplied academic source contains hard-coded Oracle connection settings. **Remove these and configure credentials externally before publishing or sharing the code.** Database-dependent features require an accessible Oracle instance; the university endpoint may not be reachable outside its environment.

### Run the Embedded Module (RISC-V)

```bash
cd stationMngt
make
make run
```

The supplied Makefile expects `riscv32-linux-gcc` and `qemu-riscv32`, with a `QEMU_SYSROOT` pointing to an environment-specific path. Update the compiler path, ABI/toolchain compatibility, and sysroot for your machine before running.

## Testing

The repository contains **54 Java test source files**, spanning controllers, domain entities, repositories, services, data structures, and utilities.

- **JUnit 4.13.2** is declared as the Java test dependency.
- **Maven Surefire 3.2.5** runs the test suite.
- **JaCoCo 0.8.10** is configured to generate coverage reports during the `test` phase.
- Tests can be launched with `mvn test`.
- After a successful test run, a JaCoCo HTML report is expected at `target/site/jacoco/index.html`.

The source archive was inspected, but this README does **not** assert a passing test count or a measured coverage percentage because neither was independently verified.

## Design & Technical Highlights

- **Custom implementations of data structures and graph algorithms:** useful for understanding algorithmic trade-offs rather than relying solely on frameworks.
- **Spatial indexing at scale:** station datasets provide a realistic setting for balanced-tree indexing and geographic searches.
- **Cross-language integration:** Java application logic, Oracle PL/SQL, and C/RISC-V modules address separate parts of the same railway domain.
- **Documented software process:** the repository includes user-story requirements, analysis artefacts, complexity discussions, and Scrum planning files.

## Known Limitations

- The Java console menu includes an option labelled `10` for max-flow/hub functionality, but **the main `switch` statement does not currently handle option 10**. This feature has supporting source code but is not reachable through that menu option as supplied.
- The Java main menu labels options 7 and 8 differently from the corresponding method calls; verify or correct their mappings before a public demo.
- Oracle integration depends on external configuration and connectivity.
- The RISC-V Makefile contains a machine-specific sysroot path and may require local adaptation.
- This is an academic proof of concept, not a production railway operations or safety system.

## Academic Context

| Field | Details |
| --- | --- |
| Project | Logistics on Rails — Integrative Project (3rd Semester) |
| Institution | ISEP — Instituto Superior de Engenharia do Porto |
| Degree | B.Sc. in Computer Engineering |
| Academic year | 2025/2026 |
| Courses | LAPR3, ESINF, BDDAD, ARQCP, FSIAP |
| Group | G093 |
| Team | Tomás Pinho, Gonçalo Afonso, Gonçalo Pinto, José Cardoso, Rafael Capão |
| Methodology | Scrum; three incremental sprints |

### Individual Contributions

This repository was developed as a team project. Individual implementation responsibilities should be described only after they have been checked against the team's task allocation and commit history.

## Publication Checklist

Before making this repository public:

1. **Rotate/revoke any still-valid database credentials** exposed in the project and remove secrets from the source (including relevant Git history).
2. Replace hard-coded database settings with environment variables or a local, ignored configuration file; provide a safe example configuration.
3. Check datasets, assignment materials, third-party assets, and team contributions for redistribution permission.
4. Fix the console menu mappings and run Java tests and representative end-to-end workflows.
5. Verify the RISC-V build instructions on a supported toolchain.

---

*Academic project for educational and portfolio purposes.*
