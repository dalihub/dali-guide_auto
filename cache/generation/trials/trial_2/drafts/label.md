[36mCompleted installing Cline SR(1.19.1) extension from VSIX.Please reload Visual Studio Code to enable it.[0m
Repaired the label.md document by fixing issue label-T1-0009 (LITERAL_NEWLINE_ESCAPE_IN_CODE) at line 147.

**Change made:**
- Replaced `"Multi-line text\nwith double spacing"` with `R"(Multi-line text\nwith double spacing)"` using a C++11 raw string literal with an actual line break inside the fenced code block.

The fix converts the broken literal `\n` escape text into proper formatting with a real newline character inside the code block, which is valid C++ syntax and clearly demonstrates multi-line text content.
