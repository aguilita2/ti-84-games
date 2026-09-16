# Wormy Technical Overview
An indepth technical overview to help new TI Basic developers understand game flow and TI Basic commands.

 ## 1\. What the program does

This is a small arcade style game called **WORMY!**:

- The screen is configured as a 16×9 grid.
- The worm starts with a length of 3.
- There are 13 apples placed randomly on the board.
- Arrow keys change direction.
- The worm moves one grid square per loop.
- Eating an apple increases the worm length and score.
- Running into the boundary or the worm's own body ends the game.
- `CLEAR` exits the program.
- After game over, pressing a key returns to the title screen.

 The program uses a **circular buffer of 144 positions** (`L₁` and `L₂`) to store the worm's body. This is a particularly important part of understanding the implementation.

---

 # 2\. The graphics coordinate system

 This is one of the most important parts of the program.

```
RectGC
...
0→Xmin
264→Xmax
0→Ymin
164→Ymax
```

 The program establishes a graphics coordinate space approximately covering:

```
X = 0 ... 264
Y = 0 ... 164
```

 It then creates a game board whose actual cells are:

```
16 columns × 9 rows
```

 Each cell is 16 pixels wide/high.

 The worm/apple center coordinates are calculated as:

```
8+16*(column-1) → A
26+16*(row-1)    → B
```

 So, for example, column 1 is:

```
X = 8
```

 column 2:

```
X = 24
```

 column 3:

```
X = 40
```

 etc.

 Likewise, row 1 is:

```
Y = 26
```

 row 2:

```
Y = 42
```

 etc.

 The offset of 8 and 26 places the worm/apple roughly in the **center of each 16×16 cell**.

---

 # 3\. The board-drawing code

 This:

```
For(I,0,16)
Line(16I,18,16I,162,0,24)
End
```

 draws the vertical grid lines.

 Since `I` goes from 0 through 16:

```
0, 16, 32, 48, ... 256
```

 there are 16 columns.

 Similarly:

```
For(I,0,9)
Line(0,18+16I,256,18+16I,0,24)
End
```

 creates the horizontal grid lines:

```
18
34
50
66
82
98
114
130
146
162
```

 Thus the playable area is a **16 × 9 grid**.

---

 # 4\. How the worm is stored

 This is arguably the most interesting programming technique in the program.

 The worm's coordinates are stored in two lists:

```
L₁
L₂
```

 `L₁` contains the **X/grid-column coordinate**, while `L₂` contains the **Y/grid-row coordinate**.

 Initially:

```
{H-2,H-1,H}→L₁
{J,J,J}→L₂
```

 So if:

```
H = 8
J = 5
```

 the worm starts at:

```
(6,5)
(7,5)
(8,5)
```

 The other two lists:

```
L₃
L₄
```

 contain the apple locations.

 So conceptually:

```
L₁/L₂ = worm coordinates
L₃/L₄ = apple coordinates
```

---

 # 5\. Why the lists have 144 entries

 The board contains:

```
16 × 9 = 144
```

 possible positions.

 The program creates:

```
seq(0,I,1,144)→L₁
seq(0,I,1,144)→L₂
```

 and later uses the indices cyclically.

 This section is the key:

```
P-I+1→Q

If Q<1
Q+144→Q
```

 `P` is effectively the **head position** in the circular buffer.

 If the calculated index goes below 1, the program wraps around to the end of the list.

 For example:

```
Q = 0
```

 becomes:

```
Q = 144
```

 This means the worm's body can be stored in a circular array without continually shifting all of its coordinates.

 That's a nice optimization for TI-Basic, where minimizing unnecessary list operations is useful.

---

 # 6\. Direction variable `D`

 The program uses:

```
D = 1   up
D = 2   down
D = 3   left
D = 4   right
```

 The arrow-key handling is:

```
If K=25 and D≠2
1→D

If K=34 and D≠1
2→D

If K=24 and D≠4
3→D

If K=26 and D≠3
4→D
```

 The `D≠...` tests prevent an immediate reversal.

 For example, if the worm is moving up (`D=1`), pressing down would be ignored.

 The actual movement happens here:

```
H→M
J→N

If D=1
N+1→N

If D=2
N-1→N

If D=3
M-1→M

If D=4
M+1→M
```

 `H,J` represent the previous head position, while `M,N` become the new head position.

---

 # 7\. Boundary collision

 The game checks:

```
If M<1
1→E

If M>16
1→E

If N<1
1→E

If N>9
1→E
```

 `E` is essentially the **game-over flag**.

```
E = 0 → game continues
E = 1 → game over
```

 Therefore the playable coordinates are:

```
M = 1..16
N = 1..9
```

---

 # 8\. Self-collision

 This section checks whether the new head overlaps the worm:

```
For(I,1,L)

P-I+1→Q

If Q<1
Q+144→Q

If L₁(Q)=M and L₂(Q)=N
1→E

End
```

 In plain English:

 > For every segment of the worm, calculate its location in the circular buffer. If that location equals the new head location, set `E` to 1.

 So the worm dies when its head enters its own body.

---

 # 9\. Apple handling

 Apple positions are held in:

```
L₃ = apple X/grid positions
L₄ = apple Y/grid positions
```

 There are 13 of them:

```
For(I,1,13)
```

 An apple is eaten when:

```
If L₃(I)=M and L₄(I)=N
```

 The program then:

```
1→A
randInt(1,16)→L₃(I)
randInt(1,9)→L₄(I)
```

 So the eaten apple is immediately relocated to a new random square.

 Then:

```
If A
Then

L+1→L
S+1→S
```

 does two things:

```
L = worm length
S = score
```

 Eating an apple therefore:

```
worm length += 1
score += 1
```

---

 # 10\. Why `P` is important

 This:

```
P+1→P

If P>144
1→P

M→L₁(P)
N→L₂(P)
```

 adds the new head to the circular buffer.

 When `P` reaches 144, it wraps back to 1.

 So:

```
1 → 2 → 3 → ... → 143 → 144 → 1 → ...
```

 The program doesn't need to move every worm segment every frame.

 Instead, it changes the head pointer and determines the body locations relative to it.

---

 # 11\. Drawing the worm

 This code:

```
For(I,1,L)
P-I+1→Q

If Q<1
Q+144→Q

8+16(L₁(Q)-1)→A
26+16(L₂(Q)-1)→B
```

 converts the stored grid coordinates into pixel coordinates.

 Then:

```
If I=L
Then
Pt-On(A,B,2,16)
Else
Pt-On(A,B,2,14)
End
```

 draws the worm.

 The distinction is:

 - `I=L` → last/oldest segment, drawn in color `16`
- otherwise → color `14`

 The precise visual interpretation depends on the TI-84 Plus CE color palette.

---

 # 12\. Color numbers

 The program uses numeric color identifiers:

```
11
14
15
16
20
24
```

 The TI-84 Plus CE reference lists the CE color palette; for example, TI's command reference identifies colors including:

```
14 = GREEN
15 = ORANGE
16 = BROWN
20 = WHITE
24 = DARKGRAY
```

  Texas Instruments Education  That gives you:

```
TextColor(15)
```

 → orange text

```
TextColor(14)
```

 → green text

```
TextColor(20)
```

 → white text

 and:

```
Pt-On(...,11)
```

 draws the apples in color 11.

---

 # 13\. Title screen

 This section:

```
Lbl S
ClrDraw
TextColor(15)
Text(38,103,"WORMY!")
...
DispGraph
```

 is the title screen.

 The interesting part is:

```
0→K
Repeat K
getKey→K
End
```

 This means:

 1. Set `K` to zero.
2. Keep reading the keyboard.
3. Exit the loop when a key produces a nonzero key code.

 Then:

```
If K=45
Stop
```

 checks whether that key was `CLEAR`.

 Otherwise execution continues to the game.

---

 # 14\. `getKey` and the keyboard

 The four arrow-key values used here are:

```
25
34
24
26
```

 and:

```
45
```

 is used for `CLEAR`.

 This is a good example of why `getKey` is different from a normal input command: it returns a **numeric key code**, allowing the program to implement real-time game controls.

 The game loop can therefore repeatedly do:

```
getKey→K
```

 without waiting for the user to enter a value.

---

 # 15\. Game loop

 The central game loop is:

```
Lbl G

While E=0

    getKey→K
    ...
    move worm
    ...
    check collisions
    ...
    draw worm
    ...

End
```

 Conceptually:

```
┌───────────────────┐
│ Read keyboard     │
└─────────┬─────────┘
          ↓
┌───────────────────┐
│ Determine heading │
└─────────┬─────────┘
          ↓
┌───────────────────┐
│ Calculate new head│
└─────────┬─────────┘
          ↓
┌───────────────────┐
│ Boundary collision│
└─────────┬─────────┘
          ↓
┌───────────────────┐
│ Body collision    │
└─────────┬─────────┘
          ↓
┌───────────────────┐
│ Move/draw worm    │
└─────────┬─────────┘
          ↓
       repeat
```

 The `Wait .067` occurs during initialization, but notably there is **no obvious `Wait` inside the main movement loop**. That is something I would investigate if your goal is to understand timing/performance. The actual speed may be governed substantially by the execution time of the loop itself.

---

 # 16\. Game-over screen

 After `E` becomes 1:

```
Lbl O
ClrDraw

TextColor(16)
Text(47,96,"GAME OVER")
...
DispGraph
```

 displays the game-over screen.

 Then the same key-waiting mechanism is used:

```
0→K
Repeat K
getKey→K
End
```

 and `CLEAR` terminates the program.

 Otherwise:

```
Goto S
```

 returns to the title screen and starts another game.

---

 # 17\. One potentially confusing point: `seq`

 For example:

```
seq(0,I,1,144)→L₁
```

 uses the `seq(` function to construct a list.

 The intent here is essentially to create a 144-element list:

```
0, 1, 2, 3, ... 144
```

 The program subsequently overwrites the relevant entries with worm coordinates.

 Similarly:

```
seq(0,I,1,13)→L₃
seq(0,I,1,13)→L₄
```

 creates 13-element lists that are subsequently filled with random apple coordinates.

---

 ## 18\. Overall structure

 The program at a higher level:

```
INITIALIZE GRAPHICS
        │
        ▼
TITLE SCREEN
        │
        ▼
WAIT FOR KEY
        │
        ├── CLEAR → EXIT
        │
        ▼
INITIALIZE GAME
        │
        ├── Create worm
        ├── Create 13 apples
        ├── Draw board
        └── Draw initial objects
        │
        ▼
GAME LOOP
        │
        ├── Read arrow key
        ├── Change direction
        ├── Calculate new head
        ├── Check boundaries
        ├── Check self collision
        ├── Add new head
        ├── Check apples
        ├── Grow worm / score
        ├── Remove old tail
        └── Draw updated state
        │
        ▼
GAME OVER
        │
        ├── CLEAR → EXIT
        │
        └── Any other key
                 │
                 ▼
             TITLE SCREEN
```