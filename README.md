# ti-84-games
A simple set of arcade style games written in TI-84 Basic to help developers learn embedded software.


# Programs

| Program | Description |
| :--- | :--- |
| `WORMY.8xp` | Eat all the apples you can (Snake-like game) |

---

## Installing the Programs

The `.8xp` files can be transferred to a compatible TI-84 calculator using **TI Connect CE**.

1. Connect the TI-84 calculator to your computer.
2. Open TI Connect CE.
3. Transfer the desired `.8xp` program to the calculator.
4. On the calculator, press `PRGM`.
5. Select the program and press `ENTER` to run it.

---

## Developer Setup: Version Controlling `.8xp` Files

This repository contains tokenized TI-84 binary files (`.8xp`). To view clean, human-readable line-by-line differences when running `git diff`, follow these steps to configure the local text converter wrapper.

### 1. Requirements
* A TI-84 Plus or compatible calculator.
* The [ti-tools](https://github.com/cqb13/ti-tools) CLI utility installed on your developer machine and accessible in your system path (`ti-tools version`).

### 2. Configure Local Git Diff Driver
Run the following commands in the root of the repository to make the helper script executable and register it with your local Git configuration:

```bash
# Make the helper script executable
chmod +x ./tools/ti84-textconv

# Configure the local Git diff driver
git config --local diff.8xp.textconv "./tools/ti84-textconv"
git config --local diff.8xp.cachetextconv true
```

Once configured, running `git diff` on any modified `.8xp` file will print the code's structural logic directly to your terminal. You can also manually inspect files outside of Git by running:
```bash
./tools/ti84-textconv CYLINDER.8xp
```

Once configured, running `git diff` on any modified `.8xp` file will print the code's structural logic directly to your terminal.
