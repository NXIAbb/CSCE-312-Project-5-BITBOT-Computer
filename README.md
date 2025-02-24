Download Link :https://programming.engineering/product/csce-312-project-5-bitbot-computer/


# CSCE-312-Project-5-BITBOT-Computer
CSCE-312 | Project 5 BITBOT Computer
In the previous projects we’ve built a computer’s basic processing and storage devices (ALU and RAM, respectively). In the labs, we also gained firsthand exposure to building the TOY computer in the labs and HACK Computer in the lectures with their underlying components. In this project we will put our knowledge and skills to work in constructing our very own BITBOT computer by putting everything together, yielding the complete BITBOT Hardware Platform. The result will be a general-purpose computer that can run many general-purpose programs that you fancy.

Below we will describe the overall design objective, the chips needing to be implemented, and recommended testing. Then we will dive into the BITBOT computer specifications. Please read the specs and examine the schematics very carefully before creating your implementation plan.

As demonstrated in lecture and lab using the HACK and TOY computers as a teaching vehicle, we recommend using a similar approach to build out the complete set of schematics on paper before diving into the respective HDL implementations.

Objective

Complete the construction of the BITBOT CPU and computer platform memory component. These components will integrate in the top-most BITBOT Computer chip. The CPU and Data Memory are already instantiated (connected) in the Computer chip but you will be required to instantiate Instruction ROM to complete the Computer chip design. Once you have designed your CPU and Data Memory, and instantiated the Instruction ROM inside the Computer, you will be able to exercise the Computer chip.

2

Chips

Chip (HDL)

Description

Testing

Memory.hdl

Entire Data Memory

Test this chip using Memory.tst and Memory.cmp

address space

CPU.hdl

The BITBOT CPU

Test this chip using CPU.tst and CPU.cmp.

Computer.hdl

The BITBOT

Test this chip using the BITBOT machine language

Computer

programs noted below.

Contract

The computer platform that you build should be capable of executing programs written in the BITBOT machine (assembly) language, specified below.

Testing

Testing the Data Memory and CPU chips: It’s important to unit-test these chips before proceeding to build the overall BITBOT Computer chip. Starter test collateral is provided. You are advised to extend them to perform robust testing.

Testing the Computer chip: A natural way to test the Computer chip implementation is to have it execute some sample programs written in the BITBOT machine (assembly) language. In order to perform such a test, one can write a test script that (i) loads the Computer.hdl chip description into the supplied Hardware Simulator, (ii) loads a machine-level program from an external file containing the program instructions into the ROM chip-part of the loaded Computer.hdl chip, and then (iii) runs the clock for enough number of cycles to execute the loaded instructions. Three such programs are provided:

Program

Comments

Add.hack

Adds two numbers at RAM[0] and RAM[1] and writes the result in RAM[2].

Mult.hack

Computes the multiplication of RAM[0] and RAM[1] and writes the result in

RAM[2].

Rect.hack

Draws a rectangle of width 16 pixels and length RAM[1] at the top left of the

screen.

The test collateral for these exercises is also included that you may use as is to test your Computer implementation. Unlike the CPU and Memory chips where we have only provided the starter collateral to be extended by you for comprehensive testing, the testing of the Computer chip will be done with the test collateral as is (i.e. you do not need to extend the Computer-level test collateral).

Finally, a nifty test collateral is provided for you to test your KEYBOARD USE (Keyboard.hack, Keyboard.tst, and Keyboard.cmp). You also do not need to modify this and may use as is.

3

BITBOT Computer Design Specifications

[A] BITBOT Computer Instructions

The BITBOT computer supports FIVE types of instructions as follows:

Arithmetic: Two arithmetic operations are supported – ADD (addition) and SUB (subtraction). Both operations support immediate and register operands.

Format:

ADD Rx, Ry, Rz //Rx = Ry+Rz, where Rx, Ry, Rz are registers SUB Rx, Ry, Rz // Rx = Ry-Rz, where Rx, Ry, Rz are registers

ADDI Rx, Ry, Constant //Rx = Ry+Constant, where Rx, Ry are registers and Constant is a 6-bit positive number between 0 and 63

SUBI Rx, Ry, Constant ////Rx = Ry-Constant, where Rx, Ry are registers and Constant is a 6-bit positive number between 0 and 63

4

Examples:

READ R0, R1 – Operation is R0 = MEM[R1]

WRITE R4, R5 – Operation is MEM[R4] = R5

Branch: Two branch operations are supported – Conditional and Unconditional

Format:

BEQ Rx, Ry //Conditional: Fetch Next Instruction from ROM[Rx] if (Ry == 0)

JMP Rx //Unconditional: Fetch Next Instruction from ROM[Rx] Examples:

BEQ R0, R5 //Fetch Next Instruction from ROM[R0] if (R5 == 0) JMP R7 //Fetch Next Instruction from ROM[R7]

Input/Output: One Input and One Output Instruction are supported – INP and OUT Format:

OUT Rx, Ry //MEM[Rx] = Ry

//Assume: Rx contents are within SCREEN Memory range INP Rx // Rx = MEM[hardwired address for Keyboard]

//Assume: Only one location for Keyboard Input will be tested

Examples:

OUT R0, R5 //MEM[R0] = R5, //R0 content is within SCREEN Memory range INP R0 // R0 = contents of Memory address dedicated to Keyboard m

[B] BITBOT Computer Instruction Encodings

Our BITBOT computer uses a Register Indirect Mode of Addressing. Unlike the HACK Computer where only the A-Register could be used to address data memory, we have a larger number of choices here. There is also no distinction made as to which register may be used to hold data versus address.

The 16-bit Instruction In[15:0] is designed to be interpreted as follows (examples are also provided along with bit encodings):

In[15:14] are OPCODE Bits

In[15:14] = 00 for Arithmetic operations

In[15:14] = 01 for Logical operations

In[15:14] = 10 for Memory or Branch operations

In[15:14] = 11 for I/O operations (OUT to the SCREEN or INP from the

KEYBOARD)

5

In[13] & In[12] are OPTYPE Bits

Arithmetic: In[13] = 0 is ADD, In[13] = 1 is SUB

Immediate Data: In[12] = 0 when no Immediate data is provided in the instruction, In[12] = 1 when Immediate data is provided in the instruction

Logical: In[13] = 0 is NAND, In[13] = 1 is NOR

Immediate Data is NOT SUPPORTED for logical operations hence In[12] is UNUSED

Memory: In[13:12] = 00 is READ, In[13:12] = 10 is WRITE

Branch: In[13:12] = 01 for Conditional Branch and In[13:12] = 11 for Unconditional Branch

I/O: In[13:12] = 00 is OUT (SCREEN), In[13:12] = 10 is INP (KEYBOARD)

In[11:9] act as follows

In[11:9] act as a 3-bit destination register address

if (OPCODE is Arithmetic) OR (OPCODE is Logical), or

if (OPCODE is Memory) AND (OPTYPE is READ), or

if (OPCODE is I/O) AND (OPTYPE is INP)

In[11:9] act as a 3-bit “pointer register” Rx address

if (OPCODE is Memory AND OPTYPE is WRITE), or

(OPCODE is I/O) AND (OPTYPE is OUT)

In[11:9] act as Address of Rx if (OPCODE is Branch)

In[8:6] and In[5:3] act as follows:

In[8:6] and In[5:3] act as SOURCE1 and SOURCE2 Register Addresses respectively if (OPCODE is Arithmetic) OR (OPCODE is Logical)

In[8:6] acts as Address of Ry if (OPCODE is Branch) and (OPTYPE is

CONDITIONAL BRANCH).

In[8:6] is the address of Ry if the instruction is Memory READ or WRITE.

In[5:0] act as follows:

In[5:0] act as Immediate operand input if OPCODE is Arithmetic.

In[5:0] are unused otherwise

Examples (Note: Spaces in bit encoding are there for readability only, and X represents don’t care or unused bit)

Arithmetic

ADD R0, R1, R2 – Operation is R0=R1+R2 and bit encoding = 0000 000 001 010 XXX

ADDI R0, R1, 7 – Operation is R0 = R1+7 and bit encoding = 0001 000 001 000111

SUB R0, R1, R2 – Operation is R0=R1-R2 and bit encoding = 0010 000 001 010 XXX

SUBI R0, R1, 7 – Operation is R0 = R1-7 and bit encoding = 0011 000 001 000111

6

Logical

NAND R0, R1, R2 – Operation is R0=R1 NAND R2 and bit encoding = 0100 000 001 010

XXX

NOR R0, R1, R2 – Operation is R0=R1 NOR R2 and bit encoding = 0110 000 001 010

XXX

Memory

READ R0, R1 – Operation is R0=RAM[R1] and bit encoding = 1000 000 001 XXXXXX

WRITE R0, R1 – Operation is RAM[R0]=R1 and bit encoding = 1010 000 001 XXXXXX

Branch

BEQ R0, R1 – Operation is to set the address of next instruction to ROM[R0] if (R1 == 0) and bit encoding = 1001 000 001 XXXXXX

JMP R2 – Operation is to set the address of next instruction to ROM[R2] (unconditional JUMP) and bit encoding = 1011 010 XXX XXXXXX

I/O

OUT R0, R1 – Operation is RAM[R0] = R1. This instruction should only access the Screen Memory MAP. Bit Encoding = 1100 000 001 XXXXXX

INP R0 – Operation is R0 = RAM[<Keyboard Register Location>]. This instruction should only access the Keyboard Memory Map. Bit Encoding = 1110 000 XXX XXXXXX

BITBOT INSTRUCTION ENCODING CHART


BITBOT Computer Resources

BITBOT Computer

The BITBOT Computer is a 16-bit architecture consisting of 16-bit Instructions and Data. Here, the 16-bit unit is also referred to as WORD. All capacities are expressed in this unit.

7

The BITBOT Computer contains a Central Processing Unit (CPU), a 32K Instruction ROM, and 40K Data Memory.

There is one output device in the form of Screen and one input device in the form of Keyboard. Both of these I/O devices are memory-mapped to the Data Memory.

BITBOT Central Processing Unit (CPU)

The CPU contains a total of 8 16-bit General Purpose Registers: R0 through R7

The CPU also contains a 16-Bit custom ALU with two 16-bit inputs XInput and YInput, and capable of performing 2s complement arithmetic. The ALU can perform arithmetic and logical operations on its data inputs. The completely functional custom ALU design is provided to you in the release collateral.

The 16-bit output appears on the “AluOut” port and contains the result of performing

Arithmetic and Logical Instructions.

Output flags such as overflow are not tracked.

PC is the 16-bit Program Counter with output “PCOut”. The PC can be reset to 0 or loaded with contents of the selected Register to implement Conditional Branch (BEQ) or Unconditional Branch (JMP) instruction.

By default, the PC increments its state by 1 on every instruction boundary.

The initial state of the PC is 0.

BITBOT Instruction ROM

The 32K Instruction ROM is organized with 32K 16-bit instruction words.

The Instruction ROM is available as a BUILT-IN CHIP so you do not have to implement it, but simply instantiate it in your BITBOT Computer implementation.

BITBOT Data Memory

The 40K Data Memory is organized with 40K 16-bit data words.

The first 32K Data words are dedicated to the RAM. The next 8K Data words are assigned to Memory-Mapped Screen Output Device. The next 1 data word is assigned to Memory-Mapped Keyboard Input Device. So practically, the data memory is “40K plus 1” word.

BITBOT I/O Devices

As noted earlier, the I/O Devices are Memory-Mapped

The output device SCREEN is mapped to 8K words in the Data Memory

The input device Keyboard is mapped to 1 Word in the Data Memory

The Screen and Keyboard chips are available as BUILT-IN chips that may simply be instantiated in your Data Memory chip Implementation.

[D] INTERFACES

CPU Interface

a. Input Pins:

Instruction: In[15:0],

Data from Data Memory: fromM[15:0],

8

Reset (when set to 1, resets the PC state to 0) b. Output Pins:

Instruction ROM Address: PCOut[15:0],

Data to Data Memory: toM[15:0],

Data Memory Write Enable: writeM,

Data Memory Address: addressM[15:0]

Data Memory Interface

Input Pins:

Data Input: toM[15:0],

Write Enable: load,

Address: addressM[15:0]

Output Pins:

Data Output: fromM[15:0]

Instruction ROM Interface

Input Pins:

Instruction ROM Address: PCOut[15:0]

Output Pins:

Instruction: In[15:0]

Custom Arithmetic Logic Unit (ALU) Interface (The fully functional ALU.hdl is provided to you in the collateral release so this information is just provided for reference)

Input Pins:

ALU Operation: AluOp[3:0]

ALU X Data Input: x[15:0]

ALU Y Data Input: y[15:0]

Output Pins

ALU Data Output: AluOut[15:0]

Overflow: overflow

9

[E] BITBOT CPU High Level Schematic


10

Implementation Tips

Complete the computer’s construction in the following order:

(Data) Memory: This chip includes three chip-parts: RAM32K, Screen, and Keyboard. All of these chips are provided as built-in chips, so there is no need to implement them. All you need to do is to implement the top-level Data Memory by instantiating and connecting these three parts together.

CPU: This chip can be constructed according to the proposed CPU schematic and aligned with the definition of BITBOT Instructions. Note that the custom ALU chip is provided to you as a collateral.

Important Advisory: Please move the files RAM32K.class and RAM32K.hdl into tools/builtInChips inside nand2Tetris folder. After that you will be able to use RAM32K for your Memory.hdl. Also use the hardware simulator instead of VScode to test this. VScode has given error in the past. So you may decide to just stick to the hardware simulator if you encounter unexplained error.

If you choose to create any custom new chips, be sure to document and unit-test them carefully before you plug them into the architecture. And then please include them in your turn-in otherwise your design will fail during our testing.

Instruction memory: Use the built-in ROM32K chip.

Computer: The top-most Computer chip can be constructed according to the proposed implementation.

Tools

All the chips mentioned in this project, including the topmost Computer chip, can be implemented and tested using the supplied Hardware Simulator. For example, the Rect program draws a rectangle of width 16 pixels and length RAM[0] at the top-left of the screen. Now here is an interesting observation: normally, when you run a program on some computer, and you don’t get the desired result, you conclude that the program is buggy. In our case though, the supplied Rect program is bug-free. Thus, if running this program yields unexpected results, it means that the computer platform on which it runs (Computer.hdl and/or some of its lower-level chip parts) is buggy. If that is the case, you have to debug your chips.

Reference Resources

The relevant readings for this project are Chapter 5, Appendix A, and Appendix B (as a reference, and use TAMU mail id to access). Be sure to participate in the labs to build the HACK computer. The principles you will learn in these labs will prepare you to succeed in building your own BITBOT computer! The resources that you need for this project are the supplied Hardware Simulator and the files provided in Project 5 collateral.

Rubrics

CPU: 60 points

Memory: 30 points

Computer: 10 points
