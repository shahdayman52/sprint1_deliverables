# Debugging and Linting Summary
*Project: DevOps Metrics Dashboard*

## Why This Matters
Unchecked code leads to broken builds and long review cycles. Running a linter locally catches style problems and likely errors before code is pushed. Using a debugger to pause the program shows what the code is actually doing, which is more reliable than guessing from `print` statements. Together these practices guard against regressions (features that worked before and break after a change).

## Core Idea
Verify before you submit. Run linters locally on every change, and use interactive debugging with breakpoints, call stacks and stack traces to find the root cause of a defect instead of adding temporary output statements.

## Key Terms
* **Linter:** A tool that statically analyzes source code, without running it, and reports style violations, suspicious constructs and probable errors.
* **Regression:** A defect in behavior that previously worked, introduced by a new change.
* **Debugger:** A tool built into the IDE that runs a program under control so it can be paused, stepped through and inspected.
* **Breakpoint:** A marker on a line of code that tells the debugger to pause program execution just before that line runs.
* **Interactive Debugging:** Running a program in the debugger, pausing at breakpoints and inspecting its state.
* **Call Stack:** The ordered list of function calls that are currently active. The most recent call is at the top and the entry point is at the bottom.
* **Stack Frame:** One entry in the call stack, holding a function's local variables and the line it is executing.
* **Stack Trace:** The printed report of the call stack at the moment an error (exception) occurred, listing each call that led to it.
* **Root Cause:** The original reason for a defect, as opposed to the place where it became visible.

## Application

### Part 1: Local Linter Usage (Pre-Submission Verification)
Every developer must run linters locally before pushing a branch and before opening a Pull Request.

1. Save all changes in the IDE.
2. Run the linter from the CLI in the project root:
   * Python files: `flake8 .`
   * JavaScript files: `npm run lint:check`
3. Read each reported warning or error. Each report gives the file, line number and rule violated.
4. Fix every reported issue in the working copy.
5. Run the linter again until it reports no problems.
6. Only then stage (`git add`), commit and push.

Rules:
* Never push code that fails the linter.
* Do not silence a warning to make the linter pass unless a reviewer agrees.
* Linting locally is required in addition to any automated checks run after the push.

### Part 2: Interactive Debugging with Breakpoints
Use breakpoints in the IDE. Do not commit `print` statements or `console.log()` calls to track execution flow.

**Inserting a breakpoint in the IDE (for example VS Code):**
1. Open the source file.
2. Click in the gutter (the margin to the left of the line numbers) next to the line where you want execution to pause. A red dot appears. Alternatively, put the cursor on the line and press `F9`.
3. Start the program in debug mode (the Run and Debug panel, or `F5`).
4. Execution runs normally until it reaches the breakpoint, then pauses before that line executes.
5. Use the controls to continue:
   * **Continue (`F5`):** run to the next breakpoint.
   * **Step Over (`F10`):** run the current line and stop at the next one.
   * **Step Into (`F11`):** enter the function called on the current line.
   * **Step Out (`Shift+F11`):** finish the current function and return to its caller.
6. Click the red dot again to remove the breakpoint.

While paused, the Variables panel shows the current values of local variables, which can be compared with what you expected.

### Part 3: Inspecting Call Stacks and Stack Traces
Use this procedure to find the root cause of an unexpected defect.

**Reading a stack trace**
1. When an error occurs, locate the stack trace in the terminal or debug console.
2. Read the final line first: it names the error type and message.
3. Read upward through the listed calls. Each entry shows a file, line number and function. The last entry before the error line is where the error was raised. Entries above it show the chain of calls that led there.
4. Identify the entries that are ForgeLine project code (not library code) and open them in the IDE.
5. Place a breakpoint at or just before the line where the error was raised.

**Inspecting the call stack while paused**
1. Run the program in debug mode until it pauses at the breakpoint.
2. Open the **Call Stack** panel. The top frame is the function currently executing. Frames below it are the callers.
3. Click a frame to move the editor to that function's line and to show that frame's local variables in the Variables panel.
4. Starting at the top frame, check whether the variable values are what you expected.
5. If a value is wrong, move to the next frame down to see which caller supplied it. Continue until you find the frame where a correct input first becomes incorrect. That location is the root cause.
6. Fix the code in the working copy, run the program again to confirm the defect is gone, run the linter, then stage and commit the fix.
