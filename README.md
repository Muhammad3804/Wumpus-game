# 🤖 Wumpus Logic Agent

A **Knowledge-Based AI Agent** that navigates a Wumpus World-style grid using **Propositional Logic** and **Resolution Refutation** to deduce safe cells — all running inside a single-file web application with no dependencies.

> Built as part of an Artificial Intelligence course assignment on Knowledge Representation and Inference.

---

## 🎮 Live Demo

Open `wumpus_agent.html` directly in any modern browser. No server, no install, no dependencies.

---

## 📸 Preview

```
┌─────────────────────────────────┐
│  🤖  │  ✓   │  💨  │  ?    │
│ AGENT│ SAFE │BREEZE│UNKNOWN│
├──────┼──────┼──────┼───────┤
│  ✓   │  ✓   │  ?   │  ?    │
│ SAFE │ SAFE │      │       │
├──────┼──────┼──────┼───────┤
│  🏁  │  💨  │  ▼   │  ?    │
│START │BREEZE│ PIT  │       │
└─────────────────────────────────┘
```

---

## 📚 What is the Wumpus World?

The Wumpus World is a classic AI problem introduced in *Artificial Intelligence: A Modern Approach* (Russell & Norvig). It is a grid-based cave environment where:

- An **agent** must navigate the cave to find **gold**
- The cave contains deadly **Pits** and a **Wumpus** monster
- The agent **cannot see** hazards directly — it only receives local percepts
- The agent must use **logical inference** to deduce which cells are safe

It is a standard benchmark for demonstrating **Knowledge-Based Agents**, **Propositional Logic**, and **Automated Reasoning**.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔲 **Dynamic Grid** | User-defined grid dimensions (3×3 up to 8×8) |
| 💣 **Random Hazards** | Pits and Wumpus randomly placed each episode |
| 🧠 **Propositional KB** | Agent maintains a live Knowledge Base of CNF clauses |
| ⚙️ **Resolution Refutation** | Automated theorem proving to verify cell safety |
| 📡 **Real-Time Percepts** | Breeze, Stench, Glitter, Bump detection |
| 🗺️ **Grid Visualization** | Color-coded cells — safe (green), unknown (gray), hazard (red/purple) |
| 📊 **Metrics Dashboard** | Inference steps, cells proven safe, moves, KB size |
| 🤖 **Auto Agent** | Watch the AI navigate autonomously with adjustable speed |
| ⌨️ **Manual Control** | Play manually using arrow keys or on-screen buttons |

---

## 🧠 How the AI Works

### 1. Knowledge Base (KB)

The agent maintains a set of **CNF (Conjunctive Normal Form) clauses** representing everything it knows about the world.

When the agent visits a cell, it **TELL**s the KB:

```
// No breeze at (3,0) → no adjacent pits
¬B_3_0
¬P_2_0
¬P_3_1

// Breeze at (2,1) → pit in one of the adjacent cells
B_2_1
{ ¬B_2_1 ∨ P_1_1 ∨ P_3_1 ∨ P_2_0 ∨ P_2_2 }
```

Biconditionals are decomposed into CNF:

```
B_r_c ⟺ (P_adj1 ∨ P_adj2 ∨ ...)

Becomes:
Forward:  ¬B_r_c ∨ P_adj1 ∨ P_adj2 ∨ ...
Backward: ¬P_adj1 ∨ B_r_c
          ¬P_adj2 ∨ B_r_c  ...
```

---

### 2. Resolution Refutation

Before entering any unvisited cell, the agent **ASK**s the KB:

> *"Is this cell safe? i.e., does KB ⊨ ¬P_r_c ∧ ¬W_r_c ?"*

This is proved by **Resolution Refutation**:

```
To prove: KB ⊨ ¬P_2_2

1. Negate the query:        add { P_2_2 } to KB
2. Resolve clause pairs:    find complementary literals
3. If empty clause □ found: contradiction detected → ¬P_2_2 is ENTAILED ✅
4. If no new clauses:       cannot prove safety → cell marked UNKNOWN
```

The resolution algorithm:

```javascript
function resolve(c1, c2) {
  // For each literal in c1, check if its negation is in c2
  // If yes → produce resolvent (c1 ∪ c2) minus the complementary pair
  // Remove tautologies (A ∨ ¬A)
}
```

---

### 3. Agent Strategy

The auto-agent follows this priority order at each step:

```
Priority 1 → Move to safe + unvisited adjacent cell   (explore!)
Priority 2 → Move to safe + visited adjacent cell     (backtrack safely)
Priority 3 → Random valid move                        (when stuck)
Special    → Grab gold if standing on it
Special    → Navigate back to start if gold collected
```

---

## 🎮 How to Play

### Setup
1. Set **Grid Rows** and **Grid Columns** (default: 4×4)
2. Set **Number of Pits** (default: 3)
3. Click **▶ INITIALIZE WORLD**

### Manual Play
| Control | Action |
|---|---|
| `↑ ↓ ← →` Arrow keys | Move agent |
| `G` key | Grab gold |
| On-screen arrow buttons | Move agent |
| `✦` button | Grab gold |

### Auto Play
1. Adjust **Speed** slider (100ms = fast, 2000ms = slow)
2. Click **▶ AUTO STEP** to start autonomous agent
3. Click **■ STOP** to pause

### Win Condition
```
Find gold ✨ → grab it (G) → return to start 🏁 → WIN 🏆
```

### Lose Condition
```
Step on a Pit ▼ or Wumpus 👹 → GAME OVER 💀
```

---

## 🗺️ Grid Cell Legend

| Color | Symbol | Meaning |
|---|---|---|
| 🔵 Blue border | 🤖 | Agent current position |
| 🟢 Green | ✓ SAFE | Cell proven safe by KB inference |
| 🔵 Dark blue | Visited | Agent has been here |
| ⚫ Dark gray | ? | Unknown — not yet visited or inferred |
| 🟣 Purple | ▼ PIT | Pit (revealed on death or game end) |
| 🔴 Red | 👹 WUMPUS | Wumpus (revealed on death or game end) |
| 🟠 Orange dot | 💨 | Breeze detected in this cell |
| 🟡 Yellow dot | 🌫️ | Stench detected in this cell |

---

## 📊 Metrics Explained

| Metric | Description |
|---|---|
| **Inference Steps** | Total resolution operations performed by the KB |
| **Cells Proven Safe** | Number of cells the agent has logically confirmed as hazard-free |
| **Moves Taken** | Total steps the agent has moved |
| **KB Clauses** | Current number of CNF clauses in the Knowledge Base |

---

## 🏗️ Project Structure

```
wumpus-logic-agent/
│
├── wumpus_agent.html       # Complete application (single file)
└── README.md               # This file
```

Everything — HTML, CSS, and JavaScript — lives in one self-contained file. No build tools, no frameworks, no npm.

---

## 🔧 Technical Implementation

### Propositional Variables

| Variable | Meaning |
|---|---|
| `P_r_c` | There is a Pit at row r, column c |
| `W_r_c` | There is a Wumpus at row r, column c |
| `B_r_c` | Agent perceives Breeze at row r, column c |
| `S_r_c` | Agent perceives Stench at row r, column c |

### CNF Conversion

All sentences are stored in CNF (arrays of literal arrays):

```javascript
// Each clause is an array of literals
kb = [
  ["¬P_3_0"],           // unit clause: no pit at (3,0)
  ["¬W_3_0"],           // unit clause: no wumpus at (3,0)
  ["¬B_2_1", "P_1_1", "P_3_1", "P_2_2"],  // breeze biconditional
]
```

### Resolution Engine

```javascript
// Refutation: prove literal by contradiction
function askKB(literal) {
  const negation = negate(literal);
  const clauses = [...kb, [negation]];   // add negated query
  
  while (true) {
    for each pair (Ci, Cj) in clauses:
      resolvent = resolve(Ci, Cj)
      if resolvent == [] → return TRUE   // contradiction found!
    if no new clauses → return FALSE     // cannot prove
  }
}
```

---

## 🧪 Example Inference Trace

```
Agent at (3,0) — start cell
→ No breeze, no stench
→ KB learns: ¬P_2_0, ¬W_2_0  (cell above is safe)

Agent moves to (2,0)
→ No breeze, no stench  
→ KB learns: ¬P_1_0, ¬W_1_0, ¬P_2_1, ¬W_2_1

Agent moves to (2,1)
→ BREEZE detected!
→ KB learns: B_2_1
→ Adds clause: { ¬B_2_1 ∨ P_1_1 ∨ P_2_2 ∨ P_2_0 ∨ P_3_1 }
→ But KB already knows ¬P_2_0 (from earlier)
→ Resolution eliminates P_2_0 from clause
→ Remaining candidates: P_1_1, P_2_2, or P_3_1

Agent does NOT enter unknown cells adjacent to breeze
→ Backtracks to safe cells and explores elsewhere
```

---

## 📖 Concepts Demonstrated

- **Knowledge-Based Agents** — agent that reasons from a KB
- **Propositional Logic** — sentences, literals, clauses
- **CNF Conversion** — biconditionals → implication → CNF
- **Resolution Refutation** — proof by contradiction
- **Entailment** — KB ⊨ α via resolution
- **Forward Chaining** — facts propagated through KB automatically
- **Heuristic Search** — agent prioritizes safe unexplored cells

---

## 📝 References

- Russell, S. & Norvig, P. — *Artificial Intelligence: A Modern Approach* (Chapter 7 — Logical Agents)
- Wumpus World specification — AIMA Figure 7.2

---

## 👤 Author

Built for AI Course Assignment — Knowledge-Based Agents & Propositional Logic

---

## 📄 License

MIT License — free to use, modify, and distribute.
