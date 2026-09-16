# TI Basic Programming Quick Reference Guide
Learning a new programming language can be tough. This quick reference guide points to vendor documentation and some basics of TI Basic.

 ## Official TI Documentation

 The most useful official references are:

 - [TI-84 Plus CE Reference Guide — Commands and Functions Listing](https://education.ti.com/html/webhelp/EG_TI84PlusCE/EN/Subsystems/e-guide_ref84plus_en/content/m_appxa/aa_commandlist.HTML)  — the best reference for individual commands and their syntax. TI specifically describes this as the command/function reference for creating TI-Basic programs.  Texas Instruments Education
- [TI-Basic Programming Guide](https://education.ti.com/html/eguides/graphing/84PlusCE-TPy/EN/content/eg_84tprgm/m_splashpage/ti-progguide_ce.HTML), [TI-84 Plus CE TI-Basic Programming Guide](https://education.ti.com/html/webhelp/EG_TI84PlusCE/EN/Subsystems/e-guide_prog_en/Content/M_SplashPage/TI-ProgGuide_CE.HTML) — explains program structure, control instructions, I/O, color commands, etc.  Texas Instruments Education
- [TI-84 Plus CE eGuide](https://education.ti.com/html/eguides/graphing/84PlusCE/EN/content/home_84plusce_v6.0.HTML)  — broader calculator documentation, including programming and graphing.
- [TI-84 Plus CE Guidebooks](https://education.ti.com/en/guidebook/details/en/3BBF042421644CE2AF713484B03A8B11/ti-84-plus-ce) — TI's collection of current manuals and PDFs.
- [TI-84 Plus CE Reference Guide PDF](https://education.ti.com/download/en/ed-tech/1424CF4F539A4DBB9145E2AA89F0FF54/8DB4EE119C9C475DA0EAD61B98D1BA4B/GRefGuide_84PlusCE_EN.pdf)  — downloadable reference; for example, it explicitly documents `RectGC` as setting rectangular graphing coordinates.  Texas Instruments Education

---

 ## Common Keyword / Command Documentation

 Here is a command-by-command explanation of the significant TI-Basic tokens in your program.

 | Keyword | Meaning in this program |
| --- | --- |
| `RectGC` | Sets the graphing coordinate system to **rectangular** coordinates. This is important because the program subsequently sets `Xmin`, `Xmax`, `Ymin`, and `Ymax`. TI documents `RectGC` as "Sets rectangular graphing coordinates format."  Texas Instruments Education |
| `FnOff` | Turns off/deselects the `Y=` functions. This prevents graphing functions from interfering with the game's graphics.  Texas Instruments Education |
| `PlotsOff` | Disables statistical plots. |
| `AxesOff` | Hides the graph axes. |
| `GridOff` | Hides the graph grid. |
| `LabelOff` | Hides graph-axis labels. |
| `BackgroundOff` | Removes the graph background image, if one is selected. |
| `ClrDraw` | Clears the drawing/graphics screen. |
| `TextColor(` | Selects the color used by subsequent `Text(` operations. This is a **TI-84 Plus CE** color-graphics command. |
| `Text(` | Draws text at a specified pixel/graphics position. |
| `DispGraph` | Displays the graph/drawing screen. |
| `getKey` | Reads the currently pressed calculator key and returns its key-code number. |
| `Stop` | Immediately terminates the program. |
| `Lbl` | Defines a program label that can be targeted by `Goto`. |
| `Goto` | Jumps program execution to a specified `Lbl`. |
| `Repeat` | Repeats a block until its condition becomes true. |
| `While` | Repeats a block while its condition remains true. |
| `End` | Terminates a `For`, `While`, `Repeat`, or conditional block. |
| `If` | Conditional execution. |
| `Then` | Begins the block executed when an `If` condition is true. |
| `Else` | Begins the alternative block when an `If` condition is false. |
| `For(` | Creates a counted loop. For example, `For(I,1,13)` executes 13 times. TI documents the syntax as `For(variable,begin,end[,increment])`.  Texas Instruments Education |
| `randInt(` | Generates a random integer in the specified inclusive range. |
| `seq(` | Generates a sequence/list of values. Here it initializes the worm/apple lists. |
| `Line(` | Draws a line between two graphics coordinates. |
| `Pt-On(` | Turns on/draws a pixel or point at a specified graphics coordinate, with additional size/color arguments on the CE. |
| `Pt-Off(` | Erases/removes a point at a specified graphics coordinate. |
| `Wait` | Pauses execution for a specified amount of time. This is used here to control the worm's movement speed. `Wait` is among the TI-84 Plus CE's documented programming commands.  Texas Instruments Education |
| `toString(` | Converts a numerical value into a string. Here it converts the score so it can be concatenated with `"SCORE: "`. TI added/documented `toString(` in the CE programming environment.  Texas Instruments Education |
| `and` | Logical AND. Both conditions must be true. |
| `≠` | "Not equal to." |
| `→` | Store/assignment operator. For example, `0→S` stores zero in `S`. |
| `+`, `-`, `*` | Arithmetic operators. |
| `→L₁`, `→L₂`, etc. | Stores values into TI-Basic lists. |
| `{...}` | Creates a list literal, e.g. `{H-2,H-1,H}`. |

---

 ## Writing TI Basic and Tokens
TI's reference guide notes that TI-Basic commands are entered as **tokens**, rather than ordinary individual characters. The calculator's CATALOG and TI Connect CE program editor can insert these tokens.  

 ### How to Type Basic without Syntax Errors
If you type the TI Basic program into TI Connect CE editor without using catalog tokens it may results in syntax errors. For example, the `FnOff` without trailing space character will rendor as a syntax error. These tokens: `FnOff `, `PlotsOff `, `AxesOff `, `GridOff `, or `LabelOff ` expect a space character immediately following word. List tokens must actually have subscript numeral so `L₁` instead of `L1`.