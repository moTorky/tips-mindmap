resource: https://mariokartwii.com/armv8/
- A CPU doesn't understand any Human Language, but a CPU can understand two things. **0**'s and **1**'s.
- Assembly Language is a human readable form of these blocks of 0's and 1's (CPU instructions)
- _Assembler_ will take in a source text file written in a CPU's Assembly language, and will translate it into a hexadecimal representation
- if two different CPUs use a different Assembly Language, they **cannot** run each other's instructions.
- _Compiler_ takes in a source text file written in C/C++/etc, and translates it to a desired Assembly Language, so it could run on many CPUs
- special terms are used to describe certain lengths of Binary/Bit values
	8 Bits = **Byte** (2 Hex digits) Example: 0x44  
	16 Bits = **Halfword** (4 Hex digits) Example: 0xB0C8  
	32 Bits = **Word** (8 Hex digits) Example: 0xDEDD0020  
	64 Bits = **Double-Word** (16 Hex digits)  
	128 Bits = **Quadword** (32 Hex digits)
- ARMv8 AArch64 Instruction Length  
	ARMv8 AArch64 is an Assembly language that uses the same sized Bit blocks for all of its instructions. **All** instructions are **32**-bits (word) in length.
- memory 
	- The region of Memory where a CPU's instructions reside at is usually called **Static** or **Main** memory. named _Static_ because every time a specific program is executed by the CPU, the CPU instructions are usually placed at the same locations
	- The region of Memory where Data is kept at is usually called **Dynamic** or **Heap** memory. It's called _Dynamic_ because every time a specific program is executed/launched by the CPU, the Data may **not** be at the same location
- Registers
	- Registers can be categorized into 3 main groups..
		- General (General Purpose Registers, aka GPRs)
		- Float (Floating Point Registers, aka FPRs)
		- Special (Special Purpose Registers aka SPRs)
	- ARMv8-A has **31 general-purpose registers**: `x0` to `x30`
	- Each has a 64-bit (`x`) and 32-bit (`w`) version (e.g. `x0` / `w0`)

| Register      | Role                                                                                                                                                |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `x30`         | **Link Register (LR)** – Remembers **where to return** after a function call                                                                        |
| `x29`         | **Frame Pointer (FP)** – Points to the base of the **current function's stack frame**                                                               |
| `sp`          | **Stack Pointer** – Always points to the **top of the stack**                                                                                       |
| `pc`          | **Program Counter** – Holds the address of the **current instruction** (you don’t use it directly, but the CPU does)                                |
| `xzr` / `wzr` | **Zero Register** – Pretends to be **always 0**. Writing to it throws away the value, where `xzr` for **Extended**, and `wzr` for **Non-Extended**. |
- Writing Numerical Values in a Source File
	- decimal values, there are no special requirements. For negative numbers, simply (i.e. -100)
	- hexadecimal ***must*** pre-pend the number with "0x". Example: 0xAC
	- binary ***must*** pre-pend the number with "0b". Example: 0b0101
- comments like C -> /* multi lines */      , // one line
- The GPRs have two different "_modes_". **Extended** and **Non-Extended**.
	- **Extended** when an Instruction will utilize **all** 64-bits of the GPR. 
	  **xD** D = Register's number. like -> x22  
	- Non-Extended is when an Instruction will utilize **only** the lower (righthand-side) 32-bits
	  **wD** D = Register's number. like -> w**11**.
- In every instruction, there is a _Destination Register_. witch holds the result
	- `operation destination, source1, source2`
	- `operation destination, source1`
	- `operation destination, VALUE`  the value called *Immediate Values* can could be Signed, or unsigned with (64, 32, 12-bit) length
	- `operation destination`
- **Signed** vs **Unsigned** values
	- Signed meaning **negative** numbers are possible
	- a number is negative?
		1. The first bit of the value contained in the register is a 1. and...
		2. You specify the values to be treated as Signed (this is determined by conditional branches).
	- for Unsigned values it takes the entire range (0x00000001 - 0xFFFFFFFF) and 0x00000000:0
	- for Signed values it splits the range (0x80000000 thru 0xFFFFFFFF) for negative,  (0x00000001 thru 0x7FFFFFFF) for positive, and 0x00000000:0
- arithmetic operations (all operations wok for extended and nonextended just replace `x` with`w`)
	- `add xD, xA, xB`
	- _`add xD, xA, aimm`_
	- _sub xD, xA, xB //xD = xA - xB_
	- _sub xD, xA, aimm //xD = xA - aimm_
	- **NOTE:** There are no forms of the Divide instructions that permits Immediate Value implementation
	- _sdiv xD, xA, xB //Signed Division. xD = xA/ xB_
	- _udiv xD, xA, xB //Unsigned Division. xD = xA / xB_
	- _mul xD, xA, xB //Signed Multiplication. xA * xB = xD_
	- _smull xD, wA, wB //Signed multiplication Long. Multiply two non-extended register values, Result in extended register  
	- umull xD, wA, wB //Unsigned multiplication Long. Multiply two non-extended register values, Result in extended register_
	- _neg xD, xA_ // if xA have 100 , then xD will have -100
	- _mov xD, SIMM32 //0xFFFFFFFFFFFF8000 thru 0x0000000000007FFF_
	- _mov xD, xA_
- write to register
	1. Literal Pools:	`ldr reg, =value` 
	```armasm
	// load 0x5FFF0FFF into w10
	ldr w10, =0x5FFF0FFF
	// load 0x111122223333CCCC into x16  
	ldr x16, =0x111122223333CCCC
	```
	2. Using "move" type instructions (`mov`, `movk`, `movz`)
		- `movz` = Move with zeroing (clears the rest of the register)
		- `movk` = Move and keep (only replaces the 16-bit portion specified)
		- `movn` works like `movz`, but writes the bitwise NOT of the immediate.
	```armasm
	// xD = Register  
	// Value = 0xWWWWXXXXYYYYZZZZ  
	movz xD, 0xWWWW, lsl #48  // write WWWW start after 48-bit an full reg with 0
	movk xD, 0xXXXX, lsl #32  // write XXXX start after 32-bit 
	movk xD, 0xYYYY, lsl #16  // write XXXX start after 16-bit 
	movk xD, 0xZZZZ
	```
	note that `movz` can be replaced with `mov xD, 0xWWWW000000000000`, 
	or: `movk xD, 0xXXXX, lsl #48`, but `movz` fill reg with 0
	-  Using `orr` to Move a Value Between Registers
		```armasm
		// Copy the value of x1 into x0 using ORR
		orr x0, xzr, x1
		```
- 