
Download Link :https://programming.engineering/product/this-project-will-feel-somewhat-familiar/

# CMSC216
This project will feel somewhat familiar
1 Introduction
This project will feel somewhat familiar in that it is nearly identical to the preceding project: there is a coding problem and a puzzle-solving problem. The major change is that everything is at the assembly level:
• Problem 1 re-works the Thermometer functions from the previous project in x86-64 Assembly rather than C
• Problem 2 involves analyzing a binary executable to provide it with the correct input to “defuse” the executable much like the previous project’s Puzzlebox problem
Working with assembly will get you a much more acquainted with the low-level details of the x86-64 platform and give you a greater appreciation for “high-level” languages (like C).
2 Download Code and Setup
Download the code pack linked at the top of the page. Unzip this which will create a project folder. Create new files in this folder. Ultimately you will re-zip this folder to submit it.
File State Notes
Makefile Provided Problem 1 Build file
thermo.h Provided Problem 1 header file
thermo_main.c Provided Problem 1 main() function
thermo_sim.c Provided Problem 1 thermometer simulator functions
thermo_update_asm.s CREATE Problem 1 Assembly functions, re-code C in x86-64, main file to edit for problem 1
thermo_update.c CREATE Problem 1 C functions, COPY from Project 2 or see a staff member to discuss

test_thermo_update.c Testing Problem 1 testing program for thermo_update_asm.c
test_thermo_update_asm.s Testing Problem 1 testing program for thermo_update_asm.c
test_thermo_update.org Testing Problem 1 testing data file
testy Testing Problem 1 test running script
puzzlebin Provided Problem 2 Executable for debugging
input.txt EDIT Problem 2 Input for puzzlebox, fill this in
3 Problem 1: Thermometer Assembly Functions
The functions in this problem are identical to a previous assignment in which code to support digital thermometer was written. These functions are:
int set_temp_from_ports(temp_t *temp)
Read global variables corresponding to sensor and mode information and set the fields of a temp_t structure accordingly.
int set_display_from_temp(temp_t temp, int *display)
Given a temp_t struct, reset and alter the bits pointed to by display to cause a proper temperature display.
int thermo_update()
Update global THERMO_DISPLAY_PORT using the above functions.
The big change in this iteration will be that the functions must be written in x86-64 assembly code. As C functions each of these is short, 30-50 lines maximum. The assembly versions will be somewhat longer as each C line typically needs 1-4 lines of assembly code to implement fully. Coding these functions in assembly give you real experience writing working assembly code and working with it in combination with C.
The code setup and tests are identical for this problem as for the previous C version of the problem. Refer to original Thermometer Problem description for a broad overview of the thermometer simulator and files associated with it.
3.1 Hand-Code Your Assembly
As discussed in class, one can generate assembly code from C code with appropriate compiler flags. This can be useful for getting oriented and as a beginning to the code your assembly versions of the functions. However, code that is clearly compiler-generated with no hand coding will receive 0 credit.
• No credit will be given on manual inspection
• Penalties will be assessed for Automated Tests which lower credit to 0
Do not let that dissuade you from looking at compiler-generated assembly code from you C solution to the functions. Make sure that you take the following steps which are part of the manual inspection criteria.
Base your Assembly code on your C code
The files to be submitted for this problem include
• thermo_update.c: C version of the functions
• thermo_update_asm.s: Assembly version of the functions
Graders may examine these for a correspondence between to the algorithm used in the C version to the Assembly version. Compiler generated assembly often does significant re-arrangements of assembly code with many intermediate labels that hand-written code will not have.
If you were not able to complete the C functions for the Project 2 display problem from the previous project, see a course staff member during office hours who will help you get them up and running quickly.
Annotate your Assembly Thoroughly
Comment your assembly code A LOT. While good C code can be quite self-explanatory with descriptive variable names and clear control structures, assembly is rarely so easy to understand. Include clear commentary on your assembly. This should include
• Subdividing functions into smaller blocks with comments describing what the blocks accomplish.
• Descriptions of which “variables” from the C side are held in which registers.
• Descriptions of most assembly lines and their effect on the variables held in the registers.
• Descriptions of any data such as bitmasks stored in the assembly code.
• Use informative label names like .ROUNDING_UP to convey further meaning about what goals certain positions in code are accomplishing.
Use Division
While it is a slow instruction that is cumbersome to set up, using idivX division instruction is the most human-readable means to compute several results needed in the required functions. Compiler generated code uses many tricks to avoid integer division so a lack of idivX instructions along this line will be a clear sign little effort has been put into the assembly code.
3.2 General Cautions when coding Assembly
1. Get your editor set up to make coding assembly easier. If you are using VS Code, the following video will show you how to install an extension to do syntax highlighting and block comment/uncomment operations in assembly: https://youtu.be/AgmXUFOEgIw
2. Be disciplined about your register use: comment what “variables” are in which registers as it is up to you to keep track. The #1 advice from past students to future students is “Comment the Crap out of your assembly code” on this project.
3. Be Careful with constants: forgetting a $ in constants will lead to a bare, absolute memory address which will likely segfault your program. Contrast:
movq $0,%rax # rax = 0
movq 0, %rax # rax = *(0): segfault
# bare 0 is memory address 0 – out of bounds
Running your programs, assembly code included, in Valgrind can help to identify these problems. In Valgrind output, look for a line number in the assembly code which has absolute memory addresses or a register that has an invalid address.
4. Recognize that in x86-64 function parameters are passed in registers for up to 6 arguments. These are arranged as follows
1. rdi / edi / di (arg 1)
2. rsi / esi / si (arg 2)
3. rdx / edx / dx (arg 3)
4. rcx / ecx / cx (arg 4)
5. r8 / r8d / r8w (arg 5)
6. r9 / r9d / r9w (arg 6)
and the specific register corresponds to how argument sizes (64 bit args in rdi, 32 bit in edi, etc). The functions you will write have few arguments so they will all be in registers.
5. Use registers sparingly. The following registers (64-bit names) are “scratch” registers or “caller save.” Functions may alter them freely (though some may contain function arguments).
rax rcx rdx rdi rsi r8 r9 r10 r11 # Caller save registers
No special actions need to be taken at the end of the function regarding these registers except that rax should contain the function return value.
Remaining registers are “callee save”: if used, their original values must be restored before returning from the function.
rbx rbp r12 r13 r14 r15 # Callee save registers
This is typically done by pushing the callee registers to be used on the stack, using them, them popping them off the stack in reverse order. Avoid this if you can (and you probably can in our case).
6. Be careful to adjust the stack pointer using pushX/popX or subq/addq . Keep in mind the stack must be aligned to 16-byte boundaries for function calls to work correctly. Above all, don’t treat rsp as a general purpose register.
3.3 Register Reference Diagram
For reference, here is a picture that appears in the lecture slides that summarizes the names and special uses for the registers in x86-64.

Figure 1: Summary of general purpose register usages in x86-64.
3.4 Structure of thermo_update_asm.s
Below is a rough outline of the structure of thermo_updat_asm.s. Consider copying this file as you get started and commenting parts of it out as needed.
### Begin with functions/executable code in the assmebly file via ‘.text’ directive
.text
.global set_temp_from_ports

## ENTRY POINT FOR REQUIRED FUNCTION
set_temp_from_ports:
## assembly instructions here

## a useful technique for this problem
movX SOME_GLOBAL_VAR(%rip), %reg
# load global variable into register
# Check the C type of the variable
# char / short / int / long
# and use one of
# movb / movw / movl / movq
# and appropriately sized destination register

## DON’T FORGET TO RETURN FROM FUNCTIONS

### Change to definint semi-global variables used with the next function
### via the ‘.data’ directive
.data

my_int: # declare location an single integer named ‘my_int’
.int 1234 # value 1234

other_int: # declare another int accessible via name ‘other_int’
.int 0b0101 # binary value as per C

my_array: # declare multiple ints sequentially starting at location
.int 20 # ‘my_array’ for an array. Each are spaced 4 bytes from the
.int 0x00014 # next and can be given values using the same prefixes as
.int 0b11110 # are understood by gcc.

## WARNING: Don’t forget to switch back to .text as below
## Otherwise you may get weird permission errors when executing

.text
.global set_display_from_temp

## ENTRY POINT FOR REQUIRED FUNCTION
set_display_from_temp:
## assembly instructions here

## two useful techniques for this problem
movl my_int(%rip),%eax # load my_int into register eax
leaq my_array(%rip),%rdx # load pointer to beginning of my_array into rdx

.text
.global thermo_update

## ENTRY POINT FOR REQUIRED FUNCTION
thermo_update:
## assembly instructions here
3.5 set_temp_from_ports
int set_temp_from_ports(temp_t *temp);
// Uses the two global variables (ports) THERMO_SENSOR_PORT and
// THERMO_STATUS_PORT to set the fields of `temp`. If
// THERMO_SENSOR_PORT is negative or above its maximum trusted value
// (associated with +45.0 deg C), this function sets the
// tenths_degrees to 0 and the temp_mode to 3 for `temp` before
// returning 1. Otherwise, converts the sensor value to deg C using
// shift operations. Further converts to deg F if indicated from
// THERMO_STATUS_PORT. Sets the fields of `temp` to appropriate
// values. `temp_mode` is 1 for Celsius, and 2 for Fahrenheit. Returns
// 0 on success. This function DOES NOT modify any global variables
// but may access them.
//
// CONSTRAINTS: Uses only integer operations. No floating point
// operations are used as the target machine does not have a FPU. Does
// not use any math functions such as abs().
Note that this function uses a temp_t struct which is in thermo.h described here:
// Breaks temperature down into constituent parts
typedef struct{
short tenths_degrees; // actual temp in tenths of degrees
char temp_mode; // 1 for celsius, 2 for fahrenheit, other values are errors
} temp_t;
Assembly Implementation Notes set_temp_from_ports
1. The function takes a single argument, a pointer in rdi.
2. Return values or functions are to be placed eax for 32 bit quantities as is the case here (int).
3. To access global symbols/variables which are not defined in the assembly file, use the relative position from the instruction pointer register which allows the linker to handle the task. Specifically relevant examples are
movw THERMO_SENSOR_PORT(%rip), %dx # copy global var to reg dx (16-bit word)
movb THERMO_STATUS_PORT(%rip), %cl # copy global var to reg cl (8-bit byte)
movl %r8d,THERMO_DISPLAY_PORT(%rip) # copy reg r8d to global var (32-bit long-word)
### WARNING: Not all of the above instructions belong in
### set_temp_from_ports. Determine which are relevant and which
### are useful for this function and which belong elsewhere.
4. Use comparisons and jump to a separate section of code that is clearly marked as “error” if you detect a bad arguments. Be careful to use appropriate assembly instructions for the type of data being compared.
◦ cmpX performs comparison based on subtraction; pick cmpq / cmpl / cmpw / cmpb according to the size of data being compared.
◦ Jump instructions such as jg assume prior comparison was done on signed quantities.
◦ Jump instructions like ja assume prior comparison was done on unsigned quantities.
5. To do the initial temperature conversion of the temperature sensor one must divide by 32. Avoid the division and use a shift instead. The remainder can also be found by masking / anding low order bits that would be shifted off which will allow for rounding.
As with your C version, convert to Celsius and perform rounding first. Then if needed, convert the rounded temperature to Fahrenheit.
6. If the temperature must be converted to Fahrenheit, make use of division instructions to achieve fahrenheit = (9 * celsius) / 5 + 32. Keep in mind that the idivX instruction must have rax as the dividend and rdx sign-extended from it. This may involve use of the following sequence of instructions:
cwtl # sign extend ax to long word
cltq # sign extend eax to quad word
cqto # sign extend rax to rdx
Any register can contain the divisor. After the instruction, rax / eax / ax will hold the quotient and rdx / edx / dx the remainder. In this function, a single division will be sufficient.
7. A pointer to a temp_t struct can access its fields using the following offset table which assume that %reg holds a pointer to the struct (substitute an actual register name).
Destination Assembly
C Field Access Offset Size Assign 5 to field
temp->tenths_degrees 0 bytes 2 bytes movw $5,0(%reg)
temp->temp_mode 2 bytes 1 byte movb $5,2(%reg)
You will need to use these offsets to set the fields of the struct near the end of the routine.
3.6 set_display_from_temp
int set_display_from_temp(temp_t temp, int *display);
// Alters the bits of integer pointed to by display to reflect the
// temperature in struct arg temp. If temp has a temperature value
// that is below minimum or above maximum temperature allowable or if
// the temp_mode is not Celsius or Fahrenheit, sets the display to
// read “ERR” and returns 1. Otherwise, calculates each digit of the
// temperature and changes bits at display to show the temperature
// according to the pattern for each digit. This function DOES NOT
// modify any global variables except if `display` points at one.
//
// CONSTRAINTS: Uses only integer operations. No floating point
// operations are used as the target machine does not have a FPU. Does
// not use any math functions such as abs().
Assembly Implementation Notes set_display_from_temp
1. Arguments will be
◦ a packed temp struct in rdi
◦ an integer pointer in rsi
2. The packed temp_t struct is entirely in the 64-bit rdi register which has the following layout.
Bits Shift
C Field Access in rdi Required Size
temp.tenths_degrees 00-15 None 2 bytes
temp.temp_mode 16-23 Right by 16 1 byte
To access individual fields of the struct, you will need to do shifting and masking to extract the values from the rdi register.
3. Use comparisons and jump to a separate section of code that is clearly marked as “error” if you detect bad fields in the temp struct argument such as temperature values that are outside the minimum/maximum values allowed for Fahrenheit or Celsius.
4. As was the case in the C version of the problem, it is useful to create a table of bit masks corresponding to the bits that should be set for each display digit (e.g. digit “1” has bit pattern 0b0000110). In assembly this is easiest to do by using a data section with successive integers. An example of how this can be done is below.
.section .data
array: # an array of 3 ints
.int 0b101 # array[0] = 0b101
.int 0b010 # array[1] = 0b010
.int 0b111 # array[2] = 0b111
spcial_const:
.int 17 # “special” constant

.section .text
.globl func
func:
leaq array(%rip),%r8 # r8 points to array, rip used to enable relocation
movq $2,%r9 # r9 = 2, index into array
movl (%r8,%r9,4),%r10d # r10d = array[2], note 32-bit movl and dest reg
movl const(%rip),%r11d # r11d = 17 (const), rip used to enable relocation
Adapt this example to create a table of useful bit masks for digits. The GCC assembler understands binary constants specified with the 0b0011011 style syntax.
5. Make sure to check for a negative temperature and adjust the display to contain a negative sign in the correct position. It may be useful to then negate a temperature below zero so that later divisions always result in positive values.
6. Make use of division instructions to compute “digits” for the tenths, ones, tens, and hundreds place for the thermometer. With cleverness, you should only need 3-4 divisions. Use these digits to reference into the table of digit bit masks you create to progressively build up the correct bit pattern for the display.
7. Use shifts and ORs to combine the digit bit patterns to create the final display bit pattern.
3.7 thermo_update
int thermo_update();
// Called to update the thermometer display. Makes use of
// set_temp_from_ports() and set_display_from_temp() to access
// temperature sensor then set the display. Always sets the display
// even if the other functions returns an error. Returns 0 if both
// functions complete successfully and return 0. Returns 1 if either
// function or both return a non-zero values.
//
// CONSTRAINT: Does not allocate any heap memory as malloc() is NOT
// available on the target microcontroller. Uses stack and global
// memory only.
Assembly Implementation Notes for thermo_update
1. No arguments come into the function.
2. Use the syntax described earlier to access global symbols/variables such as THERMO_SENSOR_PORT.
3. Call the two previous functions to create the struct and manipulate the bits of an the display. Capture the return value for each of these functions. If either of them fails (returns non-zero) then therm_update() will return 1. Calling functions and retaining values in registers across the function calls requires some care.
4. It is necessary to capture the return value from set_temp_from_display() and retain it while running set_display_from_temp(). This likely means using a callee-save register which is retained across function calls. Make sure to push/save any callee save register and then pop/restore them before returning from thermo_update().
5. Calling a function requires that the stack be aligned to 16-bytes; there is always an 8-byte quantity on the stack (previous value of the rsp stack pointer). This means the stack must be extended via pushq or subq instruction before any calls. After aligning the stack, several function calls can be made such as in the following sequence.
pushq/subq %rsp # adjust the stack pointer to make space for local
# values AND align to a 16-byte boundary

call some_func # stack aligned, call function
## return val from func in rax or eax

call other_func # stack still aligned, call other function
## return val from func in rax or eax

popq/addq %rsp # restore the stack pointer to its original value
NOTE: the specific number of pushq instructions to use or subq values to decrease %rsp is dependent on the situation. Common total adjustments are 8 bytes, 24 bytes, and 40 bytes. Pick one that fits the situation here.
6. In order to call the set_temp_from_ports() function, this function will need to allocate space on the stack for a temp_t. As described previously, this can be done via a stack adjustment, pushq or subq X,%rsp instructions. Once fresh stack space is pointed to by %rsp, it can be copied as arguments to functions that need main memory space for data such as a temp_t struct.
7. Similarly, to call the set_display_from_temp() function, one will need a packed temp_t in a register. If the preceding set_temp_from_ports() call succeeded, this packed struct can be read from memory into a register with a movX instruction. That stack space can be re-used if needed.
8. Keep in mind that you will need to do error checking of the return values from the two functions: if either of them return non-zero values, then thermo_update() must return a non-zero value. Make sure to retain earlier return values across function calls by using callee-save registers.
3.8 Notes on Partial Credit
Partial credit will be awarded in Manual Inspection for code that looks functional but did not pass tests. However, keep in mind that tests for thermo_update() rely on the previous 2 functions working correctly and thermo_main requires all functions to work correctly in conjunction. There is no partial credit available for these Automated Tests even if they may work but the preceding functions do not which causes tests to fail.
3.9 Grading Criteria for Problem 1 GRADING 60
Weight Criteria
AUTOMATED TESTS

AUOTMATED TESTS
20 make test-prob1 runs 40 tests for correctness, 0.5 points per test
test_thermo_update.c provides tests for functions in thermo_update_asm.s
There are also tests of thermo_main which uses functions from thermo_update_asm.c
MANUAL INSPECTION

10 General Criteria for all Functions
Clear signs of hand-crafted assembly are present.
Detailed documentation/comments are provided showing the algorithm used in the assembly
There is a clear relation of the code to the C algorithm used in thermo_update.c
High-level variables and registers they occupy are described.
Error checking on the input values is done with clear “error” sections and labels

10 set_temp_from_ports()
The initial division by 64 is done using a bitwise shift instruction.
Remainders from the division by 64 are obtained through bitwise-AND on the low-order bits.
Division is used to compute Fahrenheit temperature conversions.
There is a clearly documented section which updates struct fields in memory
No function calls are made that would alter the stack contents

15 set_display_from_temp()
There is a clearly documented data section setting up useful tables of bitmasks
Struct fields are unpacked from an argument register using shift operations
Division is used to compute quotients and remainders that are needed.
No function calls are made that would alter the stack contents

10 thermo_update()
The stack is extended to create space for local variables that must be passed by address and the restored before returning
The stack is correctly aligned to a 16-byte boundary to be compatible with function calls
Function calls to the earlier two functions are made with arguments passed in appropriate registers
The return value for the first function call is retained across the second function call in a callee save register or the stack
The return value is 1 if either function returns a 1 and zero otherwise
60 TOTAL for problem, 65 points possible
NOTE: Passing all tests and earning all manual inspection criteria will earn up to 5 Points of Project Makeup Credit which will offset past and future loss of credit on projects.
4 Problem 2: The Puzzlebin

4.1 Overview
2021 GDB Quick Guide/Assembly https://www-users.cs.umn.edu/~kauffman/tutorials/gdb.html
The nature of this problem is similar to the previous project’s puzzlebox: there is a program called puzzlebin which expects certain inputs from a parameter file or typed as input. If the inputs are “correct”, a phase will be “passed” earning points and allowing access to a subsequent phases. The major change is that puzzlebin is in binary so must be debugged in assembly. The GDB guide above has a special section on debugging binaries which is worth reading. The typical startup regime is:
>> gdb -tui puzzlebin
(gdb) set args input.txt # set the command line arguments
(gdb) layout asm # show disassembled instructions
(gdb) layout regs # show the register file
(gdb) break phase01 # break at the start of the first phase01
(gdb) run # get cracking
Below is a summary of useful information concerning the puzzlebin.
Input File
Data for input should be placed in the input.txt file. The first value in this file will be the userID (first part of your UMD email address) which is 8 or fewer characters.
UserID Randomization
Each phase has some randomization based on the UserID so that the specific answers of an one students will not necessarily work for another student.
One Phase Input per Line
Place the input for each phase on its own line. Some input phases read a whole line and then dissect it for individual data. Putting each input on its own line ensures you won’t confuse the input processing.
Defusing Phases Earns Points
As with the earlier puzzlebox, points for this problem are earned based on how many phases are completed. Each phase that is completed will earn points.
Use GDB to work with Puzzlebin
The debugger is the best tool to work with running the given program. It may be tempting to try to brute force the puzzlebin by trying many possible inputs but in most cases, a little exploration will suffice to solve most phases.
4.2 Puzzlebin Scoring GRADING 40
Scoring is done according to the following table.
Pts Phase Notes
5 Phase 1
5 Phase 2
7 Phase 3
8 Phase 4
7 Phase 5
8 Phase 6
10 Phase 7 Not Required
10 ??? Additional Makeup credit if you can find it
40 60 Max 40 point for full credit, 20 MAKEUP Credit available
4.3 Advice
• Most of the time you should run puzzlebin in gdb as in
> gdb -tui ./puzzlebin
Refer to the Quick Guide to GDB if you have forgotten how to use gdb and pay particular attention to the sections on debugging assembly.
• Make use of other tools to analyze puzzlebin aside from the debugger. Some of these are described at the end of the Quick Guide to GDB. They will allow you to search for “interesting” data in the executable puzzlebin.
• Disassemble the executable to look at its entire source assembly code as a text file. The Quick Guide to GDB shows how to use objdump to do this. Looking at the whole source code reveals that one cannot hide secrets easily in programs.
• Feel free to do some internet research. There is a well-known “Binary Bomb Lab” assignment by Bryant and O’Hallaron, our textbook authors, that inspired Puzzlebin. It has a long history and there are some useful guides out there that can help you through rough patches. Keep in mind that your code will differ from any online tutorials BUT the techniques to defuse it may be similar to what is required to solve puzzles..
