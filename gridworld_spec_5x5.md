# RFC-GW01: Lava Crossing Grid World Environment Specification

**Status:** Informational  
**Version:** 2.4  
**Category:** Reinforcement Learning Environment Standard

---

## Abstract

This document specifies the formal interface, state representation, transition function, and reward signal for the Lava Crossing Grid World environment. The environment is a finite, fully observable, deterministic Markov Decision Process (MDP) intended as a benchmark for agents that must combine navigation with combinatorial sub-task solving. Implementations conforming to this specification are interoperable with any agent that consumes the standardised observation and action interface defined herein.

---

## 1. Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

Coordinates are expressed as `(row, col)` pairs. Row indices increase southward; column indices increase eastward. The origin `(0, 0)` is the north-west corner of the grid.

---

## 2. Environment Parameters

| Parameter | Value |
|---|---|
| Grid dimensions | 5 columns × 5 rows |
| Determinism | Fully deterministic |
| Observability | Fully observable |
| Episode structure | Episodic; single terminal per episode |
| Time limit | None |
| Step penalty | None |

---

## 3. Grid Layout

### 3.1 Cell types

The grid consists of cells of the following types:

| Type | Identifier | Description |
|---|---|---|
| Floor | `FLOOR` | Walkable. No effect on episode. |
| Wall | `WALL` | Impassable boundary. Defined as all out-of-bounds coordinates (the grid has no interior wall rows or columns). |
| Lava | `LAVA` | Walkable but terminal. Ends the episode with negative reward. |
| Door | `DOOR` | Occupies a single tile. Behaviour is state-dependent; see Section 6.3. |
| Start | `START` | Designated `FLOOR` tile. Agent is placed here at episode initialisation. |
| Goal | `GOAL` | Designated `FLOOR` tile. Entering this tile ends the episode with positive reward. |

### 3.2 Static map

```
col →   0    1    2    3    4
row ↓ ┌────┬────┬────┬────┬────┐
  0   │    │    │LAVA│    │    │
  1   │    │    │LAVA│    │    │
  2   │ S  │    │DOOR│    │ G  │
  3   │    │    │LAVA│    │    │
  4   │    │    │LAVA│    │    │
      └────┴────┴────┴────┴────┘
```

Unlabelled cells are `FLOOR`. Grid boundaries are impassable (wall-loop behaviour applies at all edges).

### 3.3 Named locations

| Name | Coordinate | Type |
|---|---|---|
| Start | (2, 0) | `START` |
| Goal | (2, 4) | `GOAL` |
| Door | (2, 2) | `DOOR` |
| Lava | (r, 2) for all r ∈ {0, 1, 3, 4} | `LAVA` |

---

## 4. State Representation

### 4.1 State tuple

A state `s` is a tuple:

```
s = (row, col, door_status)
```

where:

- `row ∈ {0, 1, 2, 3, 4}`
- `col ∈ {0, 1, 2, 3, 4}`
- `door_status ∈ { LOCKED, UNLOCKED }`

### 4.2 Valid positions

A position `(row, col)` is valid if and only if it lies within the grid bounds and the corresponding cell type is not `WALL`. All twenty-five cells in this grid are within bounds; no cell is typed `WALL`. Implementations MUST NOT place the agent outside the grid boundary at any point during an episode.

### 4.3 State space cardinality

| Component | Cardinality |
|---|---|
| Valid positions | 25 |
| Door status | 2 |
| Total \|S\| | 50 |

### 4.4 Initial state

At episode start the environment MUST initialise to:

```
s_0 = (row=2, col=0, door_status=LOCKED)
```

### 4.5 Observation

The environment MUST expose the full state tuple to the agent at every timestep. Partial observability extensions are outside the scope of this specification.

---

## 5. Action Space

### 5.1 Action set

The action space is state-dependent. The four movement actions are always available. The key submission action is a context action that is only available when the agent occupies the cell directly in front of the door.

| Action ID | Name | Description | Availability |
|---|---|---|---|
| `0` | `MOVE_NORTH` | Attempt to move to `(row−1, col)` | Always |
| `1` | `MOVE_SOUTH` | Attempt to move to `(row+1, col)` | Always |
| `2` | `MOVE_WEST` | Attempt to move to `(row, col−1)` | Always |
| `3` | `MOVE_EAST` | Attempt to move to `(row, col+1)` | Always |
| `4` | `SUBMIT_KEY(k)` | Submit four-digit key code `k` | Only at `(2, 1)` |

### 5.2 Action availability

The available action set `A(s)` is a function of the current state:

```
A(s) = { 0, 1, 2, 3 }           if (row, col) ≠ (2, 1)
A(s) = { 0, 1, 2, 3, 4 }        if (row, col) = (2, 1)
```

The agent stands "in front of the door" when it occupies `(2, 1)` — the floor tile immediately west of the door tile at `(2, 2)`. This is the unique tile from which the door can be interacted with.

Implementations MUST NOT make `SUBMIT_KEY` available from any position other than `(2, 1)`. Implementations MUST raise an error or return an invalid-action signal if `SUBMIT_KEY` is issued from any other position. This is a hard interface constraint, not a no-op.

### 5.3 Key code parameter

The `SUBMIT_KEY` action carries a single integer parameter `k` satisfying:

```
k ∈ { 0, 1, 2, …, 9999 }
```

The value of `k` MUST be interpreted as a four-digit code with leading zeros permitted (e.g., `k = 42` is equivalent to the code `0042`). Implementations MUST accept any integer in the closed interval [0, 9999] as a syntactically valid argument when `SUBMIT_KEY` is in `A(s)`.

---

## 6. Transition Function

The transition function `T(s, a) → (s', r, done)` is fully deterministic. For a given state `s = (row, col, door_status)` and action `a`, the environment MUST produce the next state `s'`, scalar reward `r`, and terminal flag `done` as specified below.

### 6.1 Movement actions (a ∈ {0, 1, 2, 3})

Let `(row', col')` denote the target cell implied by the movement action.

**Case 1 — Target is `WALL` or `DOOR` with `door_status = LOCKED`:**

```
s'   = (row, col, door_status)    # position unchanged
r    = 0
done = false
```

The agent MUST remain in its current cell. This is the wall-loop behaviour: the transition is valid and the action is consumed.

**Case 2 — Target is `FLOOR`, `START`, or `DOOR` with `door_status = UNLOCKED`:**

```
s'   = (row', col', door_status)
r    = 0
done = false
```

**Case 3 — Target is `LAVA`:**

```
s'   = (row', col', door_status)  # terminal absorbing state
r    = −1
done = true
```

**Case 4 — Target is `GOAL`:**

```
s'   = (row', col', door_status)  # terminal absorbing state
r    = +1
done = true
```

### 6.2 Key submission action (a = 4)

`SUBMIT_KEY(k)` is only reachable when `(row, col) = (2, 1)` (see Section 5.2). The door tile acted upon is always `(2, 2)`. Let `k_correct` denote the single correct key code for the episode (an implementation-defined constant in [0, 9999]).

The outcome depends solely on whether `k` matches `k_correct`. The current value of `door_status` has no effect on which case applies.

**Case 1 — Agent at `(2, 1)`, `k ≠ k_correct`:**

```
s'   = (2, 1, LOCKED)
r    = 0
done = false
```

The door becomes or remains locked, regardless of its prior status. If the door was previously `UNLOCKED`, it is re-locked. The agent MAY reissue `SUBMIT_KEY` with a different value of `k` on the next timestep.

**Case 2 — Agent at `(2, 1)`, `k = k_correct`:**

```
s'   = (2, 1, UNLOCKED)
r    = 0
done = false
```

The door becomes or remains unlocked. The agent may move east through the door tile on any subsequent step, until and unless a wrong key is submitted.

### 6.3 Door tile semantics

The door tile at `(2, 2)` behaves as follows depending on `door_status`. Because `door_status` can be toggled by `SUBMIT_KEY` in either direction, the traversability of the door tile may change multiple times within a single episode.

| `door_status` | Behaviour when agent attempts to enter |
|---|---|
| `LOCKED` | Treated as `WALL`; movement blocked (Case 1 of Section 6.1) |
| `UNLOCKED` | Treated as `FLOOR`; movement permitted (Case 2 of Section 6.1) |

---

## 7. Reward Signal

### 7.1 Reward function

```
R(s, a, s') =
  +1   if cell_type(s') = GOAL
  −1   if cell_type(s') = LAVA
   0   otherwise
```

### 7.2 Properties

- Rewards are sparse. No per-step cost is applied.
- The reward signal is bounded: `R ∈ { −1, 0, +1 }`.
- Non-zero rewards are terminal: an episode ends immediately upon receiving `r ≠ 0`.
- Key submission attempts carry zero reward regardless of correctness or number of attempts.

---

## 8. Episode Lifecycle

```
1.  Environment initialises to s_0 = (2, 0, LOCKED).
2.  At each timestep t:
    a.  Agent observes state s_t.
    b.  Agent selects action a_t ∈ A.
    c.  Environment computes (s_{t+1}, r_t, done_t) via T(s_t, a_t).
    d.  Agent receives (s_{t+1}, r_t, done_t).
    e.  If done_t = true, the episode terminates.
3.  A new episode MAY be started from s_0.
```

The environment does not impose a maximum episode length. Implementations MAY impose an external step limit for practical purposes, but such a limit is not part of this specification.

---

## 9. Implementation Requirements

Implementations of this specification:

1. MUST produce identical `(s', r, done)` tuples for identical `(s, a)` inputs across all calls within and across episodes.
2. MUST initialise every episode to the state defined in Section 4.4.
3. MUST expose the complete state tuple as the observation (Section 4.5).
4. MUST expose the available action set `A(s)` at each timestep as defined in Section 5.2.
5. MUST raise an error or return an invalid-action signal if `SUBMIT_KEY` is issued from any position other than `(2, 1)`.
6. MUST set `door_status = UNLOCKED` when `k = k_correct` is submitted at `(2, 1)`, and MUST set `door_status = LOCKED` when any `k ≠ k_correct` is submitted at `(2, 1)`, regardless of the prior value of `door_status`.
7. MUST NOT apply any reward for wall collisions or incorrect key submissions.

---

## 10. Summary of Key Constants

| Constant | Value |
|---|---|
| Grid width | 5 |
| Grid height | 5 |
| Start position | (2, 0) |
| Goal position | (2, 4) |
| Door position | (2, 2) |
| Key submission position | (2, 1) |
| Lava positions | (r, 2) for r ∈ {0, 1, 3, 4} |
| Key code domain | [0, 9999] |
| Number of valid key codes | 10 000 |
| Correct key codes per episode | 1 |
| \|S\| | 50 |
| \|A(s)\| at (2, 1) | 5 |
| \|A(s)\| elsewhere | 4 |
| Reward on goal | +1 |
| Reward on lava | −1 |
| All other rewards | 0 |
