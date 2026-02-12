# Rubik’s Cube Solver (C++/CMake)

A fast Rubik’s Cube solver written in **C++** with a clean, modular layout:

- **Model/** – cube state, moves, and encoding
- **Solver/** – search algorithms and heuristics
- **PatternDatabases/** – precomputed/look‑up heuristics (e.g., corners/edges PDBs)
- **Scanner/** – utilities to ingest or validate cube states
- **main.cpp** – example CLI entrypoint
- **CMakeLists.txt** – cross‑platform build

> Built by **Devansh Wadhvana** to explore efficient cube representations, admissible heuristics, and high‑performance search.

---

## ✨ Features

- **C++ (modern)** implementation with a compact **bit/array** cube representation.
- **Search algorithms**: IDA* / iterative deepening with admissible pattern‑database (PDB) heuristics. *(Exact algorithm set depends on what you enable in `Solver/`.)*
- **Pattern databases** for corners/edges to drastically prune the search.
- **Singmaster moves**: `U D L R F B` with optional modifiers `'` (counter‑clockwise) and `2` (double).
- **Deterministic** solutions for the same input state (given identical heuristic/tie‑breakers).
- **Cross‑platform** build via CMake (Linux/macOS/Windows).

---

## 📦 Requirements

- **CMake 3.15+**
- **C++17** (or newer) compiler: GCC, Clang, or MSVC

Optional (for development/analysis):
- Python 3 (for scripts you may add to generate/verify PDBs)
- OpenCV (only if you extend `Scanner/` to read cube colors from images/webcam)

---

## 🧰 Build

```bash
# Clone
git clone https://github.com/devansh9725/Rubik-s-Cube-Solver.git
cd Rubik-s-Cube-Solver

# Configure + build (Release recommended)
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
```

This will produce an executable in `build/` (e.g., `rubiks_solver` or the target defined in `CMakeLists.txt`).

> If you use a different generator/IDE (Visual Studio, Xcode), open the generated project and build the default target.

---

## ▶️ Usage

Run the solver with either a **scramble string** or a **cube state** description. The exact flags may vary slightly by your `main.cpp`; below are common patterns used in this repo.

```bash
# Example 1: Solve from a scramble (Singmaster notation)
./build/rubiks_solver --scramble "R U R' U'"

# Example 2: Solve from a facelet string (URFDLB order, 54 chars)
./build/rubiks_solver --state "UUUUUUUUURRRRRRRRRFFFFFFFFFDDDDDDDDDLLLLLLLLLBBBBBBBBB"

# Example 3: Limit search depth / choose heuristic
./build/rubiks_solver --scramble "L2 B U2 R2 F' D2" --maxDepth 25 --heuristic corners+edges

# Example 4: Write solution sequence only
./build/rubiks_solver --scramble "R U R' U R U2 R'" --quiet
```

Common options you can implement/expose (and are typical for this structure):

- `--scramble "<moves>"` — apply a scramble and solve the result
- `--state "<54 facelets>"` — raw state input in `U R F D L B` face order
- `--maxDepth N` — cap the IDA* depth
- `--heuristic <name>` — select PDB/combined heuristic
- `--printState` — print cube before/after
- `--quiet` — print only the final move sequence

Run with `--help` to see the flags compiled in your build.

---

## 🧱 Project Structure

```
Rubik-s-Cube-Solver/
├── Model/               # Cube representation & move application
├── Solver/              # IDA*/search logic, heuristics
├── PatternDatabases/    # Static/serialized PDBs and builders
├── Scanner/             # Optional input/validation helpers
├── main.cpp             # CLI entrypoint
└── CMakeLists.txt       # Build config
```

---

## 🧠 Design Notes

- **State representation**: pieces and orientations are encoded in compact arrays/bitfields to speed up move application and hashing.
- **Moves**: implemented as table‑driven permutations for speed (`Model/`).
- **Search**: **IDA*** (depth‑first iterative deepening on f = g + h) keeps memory low while using PDBs for strong pruning.
- **Heuristics**: corner and edge PDBs (and optionally pattern pairs) provide **admissible** lower bounds. Combine via `max()` for admissibility.
- **Goal**: solved state with canonical orientation; solution output as a Singmaster move sequence.

---

## 🧪 Verifying Correctness

- **Round‑trip test**: scramble → solve → apply solution → must return to solved state.
- **Random states**: fuzz N random scrambles of length 25 and verify solutions.
- **Heuristic validation**: ensure `h(state) ≤ true_distance(state)` for all sampled states.
- **Performance smoke tests**: measure nodes expanded and solution length distribution.

Example quick test script (bash):

```bash
for i in $(seq 1 50); do
  S=$(python - <<'PY'
import random; m=['U','D','L','R','F','B']; s=[]
for _ in range(25):
  x=random.choice(m); s.append(x if random.random()<.5 else x+"'");
print(' '.join(s))
PY
)
  ./build/rubiks_solver --scramble "$S" --quiet || exit 1
  echo "ok: $S"
done
```

---

## 📊 Performance Tips

- Build with `-O3 -DNDEBUG` (Release).
- Precompute move tables and cache PDB reads on startup.
- Use compact hashing (e.g., Zobrist or mixed‑radix) for transposition checks if you add them.
- Keep hot loops branch‑light; prefer table lookups to conditionals.

---

## 🧩 Move Notation (Singmaster)

| Token | Meaning                 |
|------:|-------------------------|
| `U`   | Up 90° clockwise        |
| `U'`  | Up 90° counter‑clockwise|
| `U2`  | Up 180°                 |
| `D` `D'` `D2` | Down moves      |
| `L` `L'` `L2` | Left moves      |
| `R` `R'` `R2` | Right moves     |
| `F` `F'` `F2` | Front moves     |
| `B` `B'` `B2` | Back moves      |

---

## 🛣️ Roadmap Ideas

- Kociemba two‑phase search as an alternative to IDA*.
- Disk‑backed or compressed PDBs; faster PDB generators.
- Parallel iterative deepening across multiple threads.
- Optional image‑based scanner using OpenCV.
- Solution optimality checker and pretty printer.

---

## 🙌 Author

**Devansh Wadhvana**

---

## 📄 License

MIT (or your preferred license). Add a `LICENSE` file if you want explicit terms.
