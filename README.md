# Rubik-s-Cube-Solver
Rubik’s Cube Solver (C++/CMake)
A fast Rubik’s Cube solver written in C++ with a clean, modular layout:

Model/ – cube state, moves, and encoding
Solver/ – search algorithms and heuristics
PatternDatabases/ – precomputed/look‑up heuristics (e.g., corners/edges PDBs)
Scanner/ – utilities to ingest or validate cube states
main.cpp – example CLI entrypoint
CMakeLists.txt – cross‑platform build

#Features
C++ (modern) implementation with a compact bit/array cube representation.
Search algorithms: IDA* / iterative deepening with admissible pattern‑database (PDB) heuristics. (Exact algorithm set depends on what you enable in Solver/.)
Pattern databases for corners/edges to drastically prune the search.
Singmaster moves: U D L R F B with optional modifiers ' (counter‑clockwise) and 2 (double).
Deterministic solutions for the same input state (given identical heuristic/tie‑breakers).
Cross‑platform build via CMake (Linux/macOS/Windows).
