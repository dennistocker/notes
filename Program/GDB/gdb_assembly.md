# gdb中显式汇编

1. disassemble /m
2. you can run gdb in Text User Interface(TUI) mode

    ```
    gdb -tui <program>
    (gdb)b main
    (gdb)r
    (gdb)layout split
    ```
    The `layout split` command divides the window into tow parts - one of them displaying the source code, the other one the corresponding assembly. A few other tricks:
    * `set disassembly-flavor intel`: if you prefer intel notation
    * `set print asm-demanagle`: demanagles C++ names in assembly view
    * `ni`: next instruction
    * `si`: step instruction
3. If you do not want to use the TUI mode, you can always do:

    ```
    x /12i $pc
    ```
    which means print 12 instructions from current program counter address - this also work with the tricks above.

    The `x /12i $pc` truck works in both gdb and cgdb, whereas "layout split" only works in gdb.
