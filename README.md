# binja_explain_instruction
This plugin adds a popup window to Binary Ninja that explains in simple English what an x86 instruction does.

### Read the Limitations section in detail before using this plugin, or you may find it confuses you more than it helps you.

## Assumed Knowledge Level
The descriptions are intended to be simple enough for a novice to understand. However, there is some previous knowledge assumed, notably that the reader understands the concepts of a register, an instruction, the stack, etc. Consider taking a look at [Beginners.re](https://beginners.re/) if you need help with the background.

## Examples
The explanations are closer to English than the notation used in Binary Ninja, but may sometimes be strangely worded due to the fact that they are programmatically generated.
```
mov edx, 0x11
----
Sets edx to 0x11
```
```
movsx eax, al
----
Sets eax to (4 sign-extended bytes from al)
```
```
leave
----
Sets rsp to rbp
Sets rbp to the 8 bytes at the top of the stack, then increments the stack pointer by 8.
```
```
add dword [rbp-0x54], 0xa
----
Copies ((the 4 bytes of memory starting at (rbp + -0x54)) + 0xa) into memory at address (rbp + -0x54) (4 bytes)
```

## Limitations
There are over 600 instructions in the current x86 instruction set. Rather than attempt to parse them all, the explanations here are generated by reading the corresponding Binary Ninja Low-Level Intermediate Language, which operates at a higher level. Unfortunately, the LLIL does not support corresponding operations for the entire x86 instruction set. A small portion of the x86 instructions with no LLIL equivalent have been added, but nowhere near enough to cover the entire instruction set. This project will aid beginners in understanding what common instructions do, and will hopefully help with some of the "What on earth does that instruction do?" moments, but will certainly not be able to replace consulting the documentation.

The Low-Level IL you may be used to seeing in the interface (accessed via the `i` hotkey) is not the LLIL that is parsed for most instructions. Instead, we use the Lifted IL (Options > Other IL Forms > Lifted IL), which is a less complicated form of the Low-Level Intermediate Language that corresponds more closely to the x86 instructions. However, for some conditional instructions, this is disadvantageous, since the conditional that determines where the jump goes is not folded into the Lifted IL instruction. To improve the clarity of the explanations for conditional jumps, we use the LLIL with the folded conditionals instead of the Lifted LLIL to generate those explanations. Also worth noting is that the correspondence between the Lifted IL and the x86 is not perfectly 1-to-1, although it is close enough that it is generally safe to make this assumption. On occasion, certain x86 instructions may correspond to multiple LLIL instructions (or vice versa), which may require consulting the LLIL view in order to understand what the explanation means.

The Low-Level IL occasionally uses temporary flags and registers to abstract application flow, in which case the explanation generated won't make any sense. Some conditional instructions such as `CMOVcc` and `SETcc` may result in large blocks of LLIL that only make sense upon consulting the CFG. Improvements are planned for this in the future, but for now it will be necessary to consult the LLIL view for such occurrences.

Since this project is based on the Low-Level intermediate language, it may provide useful results on architectures other than x86. However, this is completely untested, and unlikely to meet any reasonable standard of completeness. Pull requests to add support for additional architectures are very welcome!

The Medium Level IL functions at a significantly higher level than the Lifted IL, which means that each MLIL instruction is typically the product of several LLIL instructions. The "equivalent" MLIL for an instruction is displayed when available, as it is particularly helpful for understanding what parameters are passed to a function, but **don't assume a 1-to-1 equivalency between the MLIL and the assembly**. Use it for a helpful cross reference, or consult the MLIL view for a high-level overview of the entire program.

This plugin has only been tested on 64-bit Ubuntu 16.04. However, since it does not rely on any strictly os-dependent code, it will likely work on other platforms. Currently, on Mac OS X, there are some issues with attaching to the main Binary Ninja window that are being investigated.

## Origins
This project is a product of [NCC Group](https://www.nccgroup.trust/us/)'s 2017 summer internship program.

## Contributing
This plugin is designed to make it simple to add support for new LLIL instructions or additional architectures. See [CONTRIBUTING.md](https://github.com/ehennenfent/binja_explain_instruction/blob/master/CONTRIBUTING.md). If you come across any inaccuracies, feel free to file a pull request or create an issue.

## Open Source
This plugin incorporates [code by Ryan Stortz (@withzombies)](https://gist.github.com/withzombies/d4f0502754407b22da02664d4eb2fbae) that is used to display information about the CPU state before the selected instruction is executed. See instruction_state.py

## Dependencies
* PyQt5
* Binary Ninja
