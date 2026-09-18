# mc25_compiler## MC-25 Compiler Optimization & CI/CD Pipeline
### **Team Project 2 — Team Alpha**

**Contributors:** Hzbawit (Infrastructure & DevOps) & Haftamu Mekonen (Optimization Logic & Benchmarking)  
**Target Architecture:** MIPS32 (MARS 4.5 Simulator)  
**Course Code:** CoSc616   

---

## Project Overview
This repository contains the architecture and codebase for Phase 2 of the **MC-25 Compiler** project. The implementation moves beyond initial parsing by introducing target-level optimizations and modern automated deployment structures. 

The primary objectives for this team milestone are:
1. **Peephole Optimization Pass:** Modifying the backend generation system inside `codegen.c` to identify and remove redundant assembly logic, reducing code bloat and accelerating physical execution times.
2. **Industrial Devops Integration:** Engineering an automated local assessment validation engine (`validate.sh`) integrated seamlessly into a continuous cloud runner pipeline managed by **GitHub Actions**.

---

##  Project Structure & Module Mapping
The workspace files are organized as follows:
```text
mc25-compiler-pipeline/
├── .github/
│   └── workflows/
│       └── ci.yml             <-- Hzbawit: GitHub Actions orchestrator
├── tests/
│   ├── test1.mc               <-- Standard baseline test program
│   └── algebraic_identity.mc  <-- Test program containing redundant structures
├── ast.c                      <-- AST Node factory constructors 
├── ast.h                      <-- Single-struct ASTNode layout specification
├── codegen.c                  <-- Haftamu: MIPS engine & Peephole optimizer
├── symbol_table.c             <-- Linear variable declaration database
├── lexer.l                    <-- Flex Lexical Rule configuration file
├── parser.y                   <-- Bison YACC Grammar definition file
├── validate.sh                <-- Hzbawit: Headless validation pipeline runner
└── Makefile                   <-- Automated gcc build configuration
```

---

## Build and Local Compilation

The compilation pipeline is managed entirely through an automated `Makefile`. 

### Prerequisites
Before running or testing the compiler locally, ensure your host environment has the following software installed:
*   `gcc` / `build-essential` (C99 compiler suite)
*   `flex` (Lexical analyzer generator)
*   `bison` (Parser generator)
*   `Java Runtime Environment (JRE) 8+` (Required to invoke the headless MARS simulation jar)

### Execution Targets
Open your command terminal inside the root directory and run the following recipes:

```bash
# 1. Compile lexer scripts, grammar trees, and build the 'mc25' binary executable
make

# 2. Run the complete automated test suite locally
make test

# 3. Clean up intermediate generated files (*.c, *.h maps, binary executables)
make clean
```

---

##  Team Roles & Work Division

###  Partner B: Hzbawit (Infrastructure & Quality Assurance)
*   **Validation Script Engine (`validate.sh`):** Developed a modular script that crawls the `/tests` folder, compiles each native Mini-C script into raw MIPS assembly via the `mc25` engine, drops execution banners, routes tasks safely through a headless command line runner (`java -jar Mars4_5.jar nc`), and validates simulation return codes.
*   **CI/CD Infrastructure Design (`.github/workflows/ci.yml`):** Implemented a custom GitHub actions script that spins up a virtualized Ubuntu runner on every code push or pull request. The workflow dynamically updates system packages, provisions a Temurin JDK workspace, compiles the local binary repository, and runs automated testing validations.

###  Partner A: Haftamu Mekonen (Compiler Optimization & Evaluation)
*   **Peephole Optimization (`codegen.c`):** Integrated an algorithmic check directly into the active print streaming buffer inside the MIPS compilation engine. The loop identifies algebraic identities—such as `add $t0, $t0, $zero` or `addi $t0, $t0, 0`—and automatically drops them from the output buffer before file delivery.
*   **Performance Benchmarking:** Utilized the visual **MARS Simulator Tools ➔ Instruction Statistics** plug-in to map register loads, evaluating exact raw cycle counts before and after the peephole optimization pass to prove project efficiency margins.

---

##  DevOps Lifecycle: GitHub Actions Pipeline

When new modifications are committed and pushed up to the cloud repository, the automation workflow executes the following pipeline milestones:

```text
[ Local Git Push ] ────► [ GitHub Cloud Hook ]
                                │
                                ▼
┌────────────────────────────────────────────────────────┐
│  Initialize Ubuntu Linux Virtual Container             │
├────────────────────────────────────────────────────────┤
│  Provision JDK 17 Runtime Environment (Temurin Stack)  │
├────────────────────────────────────────────────────────┤
│  Fetch Core Packages: apt install flex bison gcc       │
├────────────────────────────────────────────────────────┤
│  Download & Cache headless Mars4_5.jar dependency      │
├────────────────────────────────────────────────────────┤
│  Execute Build Phase: make clean && make               │
├────────────────────────────────────────────────────────┤
│  Run Validation Automation Pipeline: ./validate.sh     │
└────────────────────────────────────────────────────────┘
                                │
                                ▼
  [ Success: Green Badge ]  ◀───┴───►  [ Failure: Red Alert Email ]
```

---

## Optimization Baseline Sample

The peephole optimizer intercepts basic expressions to prune redundant target commands:

**Unoptimized MIPS Output Emitted:**
```assembly
lw   t0, 4(sp)
li   \$t1, 0
add  \$t0, t0, t1    # <-- Redundant Identity Operation
sw   \$t0, x
```

**Optimized MIPS Output Emitted (Peephole Engaged):**
```assembly
lw   t0, 4(sp)
sw   \$t0, x            # Pruned 2 execution cycles from simulator
```
 mc25_compiler
