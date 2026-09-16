# WORMY — Design Specification

**Document type:** Software Design Specification
**Target platform:** TI-84 Plus CE
**Language:** TI-BASIC
**Application:** WORMY arcade game
**Source of truth:** Current working TI-BASIC implementation supplied for reverse engineering
**Status:** Baseline behavioral specification

---

# 1. Purpose

WORMY is a grid-based arcade game in which the player controls a continuously moving worm.

The player:

* controls the worm using the TI-84 Plus CE arrow keys,
* collects apples,
* grows the worm when an apple is collected,
* earns one point per apple,
* loses when the worm reaches the board boundary or collides with itself,
* may quit at any time using the `CLEAR` key.

The game presents a title/instruction screen before each game and a game-over screen after a collision.

---

# 2. Platform Requirements

The application shall run on a standard **TI-84 Plus CE** using TI-BASIC.

The application shall use the calculator's graph screen for game rendering.

The application shall operate without requiring the TI-84 Plus CE Python environment.

---

# 3. Game Board

## 3.1 Logical board

The game board shall contain:

* **16 columns**
* **9 rows**

Logical coordinates shall be:

```text
Column: 1–16
Row:    1–9
```

The board therefore contains:

```text
16 × 9 = 144
```

possible worm positions.

## 3.2 Coordinate semantics

Increasing column number moves the worm to the right.

Increasing row number moves the worm upward.

Therefore:

| Direction | Column change | Row change |
| --------- | ------------: | ---------: |
| UP        |             0 |         +1 |
| DOWN      |             0 |         -1 |
| LEFT      |            -1 |          0 |
| RIGHT     |            +1 |          0 |

---

# 4. Game Start Screen

Before each game, the application shall display a start screen.

The screen shall contain:

```text
WORMY!

13 APPLES

ARROW KEYS TO MOVE

CLEAR TO QUIT

PRESS ANY KEY
```

The displayed text shall be positioned on the graph screen according to the current implementation.

The start screen shall remain displayed until a key is pressed.

## 4.1 Start-screen input

If the pressed key is `CLEAR`:

* the program shall terminate.

If any other key is pressed:

* the game shall begin.

The key pressed to dismiss the start screen shall **not be treated as a game movement command**.

---

# 5. Initial Game State

Each new game shall initialize the following state.

## 5.1 Score

```text
Score = 0
```

## 5.2 Worm length

```text
Length = 3
```

## 5.3 Initial direction

The worm shall initially move:

```text
RIGHT
```

The game shall **begin moving automatically** without requiring the player to press an arrow key.

## 5.4 Initial worm position

A random starting position shall be selected:

```text
Column = random integer 3–14
Row    = random integer 3–7
```

The worm's initial three cells shall occupy:

```text
Column - 2
Column - 1
Column
```

at the same row.

The rightmost cell shall be the head.

Conceptually:

```text
TAIL   BODY   HEAD
 ●      ●      ●  →
```

The initial placement provides space between the worm and the board edges.

---

# 6. Apples

## 6.1 Number of apples

Each game shall contain exactly:

```text
13 apples
```

at all times.

The implementation shall maintain 13 apple positions.

## 6.2 Apple placement

Each apple shall be assigned:

```text
Column = random integer 1–16
Row    = random integer 1–9
```

Apple placement shall occur independently for each apple.

The current implementation does **not** prevent:

* two apples from occupying the same cell,
* an apple from initially occupying a worm cell.

This behavior is therefore part of the current baseline unless explicitly changed in a later specification revision.

## 6.3 Apple appearance

Apples shall be rendered as a small square marker using the current implementation's apple drawing style/color.

---

# 7. Worm Movement

The worm shall move continuously while the game is active.

Each movement cycle shall:

1. Read the keyboard.
2. Process any valid direction change.
3. Calculate the worm's new head position.
4. Test for boundary collision.
5. Test for self-collision.
6. If no collision occurs, update the worm.
7. Determine whether the new head occupies an apple.
8. Grow or move the tail accordingly.
9. Update the display.

---

# 8. Player Controls

The arrow keys shall control the worm.

| Key         | Direction |
| ----------- | --------- |
| Up Arrow    | UP        |
| Down Arrow  | DOWN      |
| Left Arrow  | LEFT      |
| Right Arrow | RIGHT     |

The TI-84 Plus CE key codes currently used are:

| Direction | Key code |
| --------- | -------: |
| UP        |       25 |
| DOWN      |       34 |
| LEFT      |       24 |
| RIGHT     |       26 |

---

# 9. Direction Changes

The worm shall not be permitted to reverse direction directly into itself.

The following transitions shall be prohibited:

```text
UP    → DOWN
DOWN  → UP
LEFT  → RIGHT
RIGHT → LEFT
```

If a prohibited reversal is requested, the current direction shall remain unchanged.

All other direction changes shall be accepted.

For example:

```text
RIGHT → UP       valid
RIGHT → DOWN     valid
UP    → LEFT     valid
UP    → RIGHT    valid
```

A direction change shall take effect on the next movement cycle.

---

# 10. Boundary Collision

The worm shall lose the game when the new head position falls outside the logical board.

Valid positions are:

```text
1 ≤ Column ≤ 16
1 ≤ Row ≤ 9
```

A game-over condition shall occur when any of the following is true:

```text
Column < 1
Column > 16
Row < 1
Row > 9
```

The worm shall **not wrap around** the board.

---

# 11. Self-Collision

After calculating a new head position, the game shall test whether that position is occupied by any existing worm segment.

If the new head position matches an existing worm segment:

```text
Game Over
```

The collision check shall occur **before the current tail is removed**.

Therefore, moving into the currently occupied tail position is treated as a collision in the current implementation.

---

# 12. Successful Movement

If the new head position is:

* inside the board, and
* not occupied by the existing worm,

the movement shall proceed.

The new head position shall be added to the worm.

The previous head becomes a body segment.

---

# 13. Apple Collection

After successful head movement, the application shall test the new head position against all 13 apples.

An apple shall be considered collected when:

```text
Apple column = New head column
AND
Apple row = New head row
```

Only one or more apples occupying the new head position are processed by the current loop.

Because apple positions are not prevented from overlapping, the current implementation's behavior should be preserved unless explicitly revised.

---

# 14. Worm Growth

When the worm collects an apple:

```text
Length = Length + 1
Score = Score + 1
```

The tail shall **not be removed** during that movement cycle.

The worm therefore grows by one segment.

The collected apple shall immediately be replaced by a new randomly positioned apple.

The total number of apples shall remain 13.

---

# 15. Normal Movement Without Apple

If the worm does not collect an apple:

* the new head shall be added,
* the oldest worm segment shall be removed.

Therefore the worm maintains its current length.

Conceptually:

```text
Before:

TAIL  BODY  BODY  HEAD
 ●     ●     ●     ●

After moving:

       TAIL  BODY  BODY  HEAD
        ●     ●     ●     ●
```

The visual position of the worm shall therefore advance by one grid cell.

---

# 16. Score

The score shall start at:

```text
0
```

The score shall increase by exactly:

```text
1 point per apple collected
```

The score shall be displayed during gameplay.

The current display format is:

```text
SCORE: 0
```

and shall be updated whenever the score changes.

---

# 17. Rendering

The application shall use the TI-84 Plus CE graph screen.

The current graph configuration establishes:

```text
Xmin = 0
Xmax = 264
Ymin = 0
Ymax = 164
```

Graphing axes, grid, labels, and plots shall be disabled.

The application shall use a rectangular graphics coordinate system.

---

# 18. Board Rendering

The logical 16×9 board shall be visually represented using a grid.

The current implementation uses:

```text
16-pixel cell spacing
```

The vertical grid lines correspond to:

```text
x = 0, 16, 32, ..., 256
```

The horizontal grid lines correspond to:

```text
y = 18, 34, 50, ..., 162
```

The grid shall visually separate the 16 columns and 9 rows.

---

# 19. Logical-to-Screen Coordinate Mapping

A logical cell shall be converted to a graph-screen position using:

```text
Screen X = 8 + 16 × (Column - 1)
Screen Y = 26 + 16 × (Row - 1)
```

Thus:

```text
Column 1 → X = 8
Column 16 → X = 248

Row 1 → Y = 26
Row 9 → Y = 154
```

This mapping shall be used consistently for:

* worm segments,
* worm head,
* apples.

---

# 20. Worm Rendering

The worm shall be rendered as small square markers.

The head shall be visually distinguishable from the body.

Current implementation:

* head uses marker style `2` and color `16`,
* body uses marker style `2` and color `14`.

The head shall represent the current worm position.

---

# 21. Apple Rendering

Each apple shall be rendered using:

```text
Marker style = 2
Color = 11
```

When an apple is relocated after collection, the new apple position shall be drawn.

When the tail moves away from an apple's location, the apple shall remain visible.

---

# 22. Display Update

The display shall be refreshed after initialization and during gameplay so that:

* worm movement is visible,
* newly collected apples disappear,
* replacement apples appear,
* the score is updated,
* the worm head/body positions remain accurate.

The current implementation uses a circular worm data structure and redraws/erases only the necessary worm segment positions rather than rebuilding the entire screen every movement cycle.

---

# 23. Worm Data Structure

The implementation shall support a maximum worm length of 144 cells.

The worm shall therefore be capable of occupying the entire board.

The current implementation uses two lists:

```text
L₁ = worm column positions
L₂ = worm row positions
```

with 144 entries.

The worm uses a circular-buffer approach.

A pointer identifies the newest head position.

This avoids requiring the program to shift every worm segment on each movement.

---

# 24. Apple Data Structure

Apple positions shall be maintained using:

```text
L₃ = apple columns
L₄ = apple rows
```

Each list shall contain 13 entries.

---

# 25. Game State

The game shall maintain at least the following logical state:

| State            | Meaning                        |
| ---------------- | ------------------------------ |
| Worm head column | Current head column            |
| Worm head row    | Current head row               |
| Direction        | Current movement direction     |
| Worm length      | Number of worm segments        |
| Score            | Number of apples collected     |
| Apples           | 13 current apple positions     |
| Game-over flag   | Whether collision has occurred |

Implementation-specific variable names are not part of the behavioral interface.

---

# 26. Game-Over Screen

When the worm collides with:

* the board boundary, or
* itself,

the active game loop shall terminate and the game-over screen shall be displayed.

The screen shall contain:

```text
GAME OVER

SCORE: <score>

PRESS ANY KEY

CLEAR TO QUIT
```

The final score shall be displayed.

---

# 27. Game-Over Input

The game-over screen shall wait for a key press.

If the player presses `CLEAR`:

```text
Terminate program
```

If the player presses any other key:

```text
Start a new game
```

The new game shall return to the start screen before initializing gameplay.

---

# 28. Program Termination

The program shall terminate when `CLEAR` is pressed from:

1. the start screen,
2. active gameplay,
3. the game-over screen.

`CLEAR` shall therefore function as the universal quit command.

---

# 29. New Game Reset

When starting a new game, all game-specific state shall be reset:

```text
Score       = 0
Length      = 3
Direction   = RIGHT
Game Over   = false
```

A new random worm starting position shall be generated.

A new set of 13 random apples shall be generated.

The previous game's worm and apple positions shall not carry over.

---

# 30. Randomization Requirements

The game shall randomize the initial worm location for each new game.

The initial worm head shall be selected from:

```text
Column 3–14
Row 3–7
```

Each apple shall be independently randomized across:

```text
Column 1–16
Row 1–9
```

No deterministic starting location is required.

---

# 31. Performance / Timing

The current implementation contains an initial:

```text
Wait .067
```

after the initial game rendering.

The main game loop does **not** currently contain an explicit `Wait` command.

Therefore, the current implementation's movement speed is determined primarily by TI-BASIC execution speed and input processing rather than a fixed FPS delay.

A future specification revision may introduce a defined movement interval if consistent game speed is desired, but that would constitute a behavioral change from the current implementation.

---

# 32. Current Behavioral Constraints

The following behaviors are intentionally documented because they are present in the working implementation:

### 32.1 No wall wrapping

Crossing an edge causes Game Over.

### 32.2 No direct reversal

The worm cannot immediately turn 180°.

### 32.3 Immediate automatic movement

The worm starts moving RIGHT automatically.

The player does not have to press an arrow key to begin movement.

### 32.4 Exactly 13 apples

There are always 13 apple slots.

Collecting an apple immediately creates a replacement apple.

### 32.5 Random apple overlap is possible

The current implementation does not check for duplicate apple positions or prevent apples from spawning on the worm.

### 32.6 Self-collision is checked before tail removal

The current behavior treats the existing worm body—including the segment that would otherwise become the tail—as occupied during collision detection.

---

# 33. User Interaction Summary

The complete user flow is:

```text
             ┌───────────────┐
             │  Start Screen │
             └───────┬───────┘
                     │
              Any key│CLEAR
                     │
              ┌──────▼───────┐
              │               │
              │   Exit        │
              │               │
              └───────────────┘

                     │Any non-CLEAR key
                     ▼
             ┌───────────────┐
             │ Initialize    │
             │ Game          │
             └───────┬───────┘
                     │
                     ▼
             ┌───────────────┐
             │ Worm moves    │◄─────────────┐
             │ automatically │              │
             └───────┬───────┘              │
                     │                      │
              collision?                    │
                /     \                     │
              yes      no                    │
               │        │                    │
               ▼        ▼                    │
        ┌──────────┐  Apple?                 │
        │ Game     │   / \                   │
        │ Over     │ yes  no                 │
        └────┬─────┘  │    │                 │
             │        │    │                 │
             │        ▼    ▼                 │
             │      Grow  Move tail          │
             │        │    │                 │
             │        └────┴─────────────────┘
             │
             ▼
      ┌────────────────┐
      │ Game Over      │
      │ Score          │
      │ Press Any Key  │
      │ CLEAR to Quit  │
      └───────┬────────┘
              │
        CLEAR │ Any other key
          ▼   │
        Exit  └──────────► Start Screen
```

---

# 34. Acceptance Criteria

A spec-driven implementation shall be considered functionally correct when all of the following are true.

### Startup

* [ ] Program displays the WORMY start screen.
* [ ] Start screen identifies 13 apples.
* [ ] Start screen identifies arrow-key controls.
* [ ] Start screen identifies CLEAR as quit.
* [ ] Program waits for a key before starting.
* [ ] CLEAR from the start screen terminates the program.

### Initialization

* [ ] Score starts at 0.
* [ ] Worm starts at length 3.
* [ ] Worm starts moving RIGHT automatically.
* [ ] Worm's initial position is randomized within the specified range.
* [ ] 13 apples are created.
* [ ] Apple coordinates are within the 16×9 board.

### Movement

* [ ] Worm continuously advances.
* [ ] UP changes movement to UP.
* [ ] DOWN changes movement to DOWN.
* [ ] LEFT changes movement to LEFT.
* [ ] RIGHT changes movement to RIGHT.
* [ ] Direct 180° reversal is rejected.
* [ ] Valid 90° turns are accepted.

### Collision

* [ ] Crossing the left boundary causes Game Over.
* [ ] Crossing the right boundary causes Game Over.
* [ ] Crossing the bottom boundary causes Game Over.
* [ ] Crossing the top boundary causes Game Over.
* [ ] Moving into an occupied worm segment causes Game Over.

### Apples

* [ ] Worm collecting an apple increases score by 1.
* [ ] Worm collecting an apple increases length by 1.
* [ ] Collected apple is replaced.
* [ ] Total apple count remains 13.

### Display

* [ ] Board grid is visible.
* [ ] Worm is visible.
* [ ] Head is visually distinguishable from body.
* [ ] Apples are visible.
* [ ] Score is visible.
* [ ] Screen updates as the worm moves.

### Game Over

* [ ] Game Over screen is displayed after collision.
* [ ] Final score is displayed.
* [ ] Any non-CLEAR key starts a new game through the start screen.
* [ ] CLEAR terminates the program.

---

# 35. Implementation Notes for Spec-Driven Development

The following should be treated as **implementation guidance rather than externally observable requirements**:

1. The TI-84 graph variables `X` and `Y` should **not** be used as persistent worm-coordinate variables because graph operations can modify them. The current working implementation consequently uses `H` and `J` for the worm head position.

2. A circular buffer of 144 positions is an efficient representation because the board contains exactly 144 cells.

3. `L₁/L₂` represent worm column/row positions.

4. `L₃/L₄` represent apple column/row positions.

5. `P` represents the circular-buffer head position.

6. `L` represents worm length.

7. `D` represents direction.

8. `S` represents score.

9. `E` represents the game-over state.

10. `M/N` represent the candidate next head position.

These variable names should **not necessarily be imposed on a future implementation** unless the goal is specifically to preserve the current TI-BASIC architecture.

---

## 36. Key Design Invariants

These are particularly useful for spec-driver-development because they can become automated or manual verification properties.

**Invariant 1 — Board position**

At every valid movement state:

```text
1 ≤ headColumn ≤ 16
1 ≤ headRow ≤ 9
```

**Invariant 2 — Worm length**

```text
3 ≤ wormLength ≤ 144
```

**Invariant 3 — Apple count**

```text
appleCount = 13
```

**Invariant 4 — Score**

```text
score = number of apples collected during the current game
```

**Invariant 5 — Movement**

Every successful movement changes the head by exactly one logical cell:

```text
|Δcolumn| + |Δrow| = 1
```

**Invariant 6 — Direction**

The direction is always one of:

```text
UP
DOWN
LEFT
RIGHT
```

**Invariant 7 — No direct reversal**

For two consecutive movement directions:

```text
newDirection ≠ opposite(previousDirection)
```

unless the direction is unchanged.

---