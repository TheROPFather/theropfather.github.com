---
layout: post
title:  "Writing a GetPID Application in x86 ASM"
date:   2013-05-13 20:16:03
categories: assembly
permalink: /asm/getpid_tutorial.html
---

Today I am writing an article of my experience creating a fairly simple application in the assembly programming language (ASM). It should be possible to follow along even if you have only a basic knowledge of ASM, though I would hope my explanation of the program is thorough enough that you can follow along with no ASM knowledge.

Before I get started I’ll describe my current ASM programming set-up. My operating system is OSX 10.8; this is important mainly because system calls will follow the FreeBSD calling convention (more on this later), instead of the generic Linux convention. I use NASM as my assembler, this is really due to personal preference and the great support online. The linker I use is the GNU Linker (LD), most people seem to use the GNU Compiler Collection (GCC) for linking, however I prefer to use the LD on its own. When GCC is used for linking it brings in its own procedures such as the “start” procedure, this can be confusing when looking at the program in a debugger as a beginner. It's worth mentioning that GCC uses LD for linking, and it is a great tool to learn further down the line.

My goal was to write an application to retrieve the current processes identification number (PID) and print it to the console. In the design phase this sounded like a simple task, I quickly realized that as a novice ASM programmer I was in for a steep learning curve.

The starting point for all ASM programs is to define the three different sections and set the entry point for LD. On OSX the entry point is given the label <em>start</em> as you can see defined below, however this will change for each operating system. To determine the label you need, when linking you will receive an error similar to this:

<blockquote>
Undefined symbols for architecture i386:
"start", referenced from:
implicit entry/start for main executable
ld: symbol(s) not found for inferred architecture i386
make: *** [cowboy] Error 1
</blockquote>

The undefined symbol is the label you need to give in place of <em>start</em> in the below code.

{% highlight nasm %} 
SECTION .data

SECTION .bss

SECTION .text

global start
start:
    nop
    nop
{% endhighlight %}

I will break this file down a <em>SECTION</em> at a time. The <em>.data</em> section is where all initialized data is stored, the concept is the same as initialized data in most other programming languages; simply a variable with a value assigned before compilation (Or assembly).</p>

The <em>.bss</em> section contains all uninitialized data, this can be likened to a variable that has its value assigned at run time. Since the data to be stored is not known a defined amount of space is reserved and a label to access the space is set. The <em>.bss</em> section in my program is used to reserve 8bytes of space, this is where the converted integer will be placed.</p>

<p>Last is the <em>.text</em> section. This is where the ASM instructions are placed, it's as simple as that. The first task in my application was locating the system call number to get the PID. The system call number will change for each operating system (OS) but on OSX 10.8 it can be found in the <em>syscall.h</em> file, this file shows the call number is 0×14.</p>

[code]
SECTION .text

global start
start:
    mov eax,0x14         ;Set system call getpid    
    int 0x80             ;Call the kernel
[/code]

<p>Following standard calling convention the return result of the call is stored in the EAX register, next task, print out the result. This is where my first issue arose if I tried to print the result to the console each byte was interpreted as an ASCII code. Instead of printing the result as a number an arbitrary assortment of ASCII characters would be printed. My first task therefore was to create a solution that would determine the ASCII code of each digit in the returned integer for use with the <em>sys_write</em> call.</p>

<p>A look-up table was the best solution for the conversion of a digit to its ASCII code, the look-up table contains a <strong><span style="text-decoration:underline;">string</span></strong> of numbers ranging 0-9. As to why this works should make perfect sense in the context of the code.</p>

[code]
SECTION .data   
    LookUpDig db "0123456789"
[/code]

<p>To use this table I need to be able to "index" into it (Imagine an array). If an index of 5 was used can you guess where in the table I would end up? That's right! I would land on 5, but I would get the ASCII code for 5 and not the number 5, this is because the table defines a <strong><span style="text-decoration:underline;">string</span></strong> not an <strong><span style="text-decoration:underline;">integer</span></strong>.</p>

<p>So the task was to convert the returned integer into a string, the problem is the integer returned from the <em>get_pid</em> system call looks like this: <em>50223.</em> This can't be used as an index since the table only contains 10bytes, I needed to access each digit individually: <em>5 0 2 2 3. </em>A method to achieve this was not immediately obvious, luckily for me we live in a world of math. The solution is to divide the full number by 10 and store the remainder, if this is repeated until 0, each digit can be grabbed on its own.</p>

<pre>
50223/10 = 5022.3 ← store the remainder = 3.
5022/10 = 502.2 ← store the remainder = 2.
</pre>

<p>To do this in ASM I used the DIV operation, it was perfect since it stores the remainder and keeps the result after division meaning I could make a neat loop for conversion. Here is the usage for the DIV instruction in x86 assembly:</p>

<blockquote>
DIV reg32 - EAX will be divided by the number stored in the reg32 for example: DIV EBX = EAX/EBX
</blockquote>

<p>The returned result of <em>get_pid </em>is already stored in EAX so all I need to do is pass the divisor to the DIV instruction.</p>

[code]
SECTION .bss    
    PID: resb 8      ;Reserve space for the result
[/code]

<p>Here I reserve 8bytes of space for the converted number to be stored in (Buffer).</p>

[code]
start:
    mov eax, 0x14    ;GET_PID call
    int 0x80         ; Call
    mov ebx, 0xA     ; Set divider to 10
    lea ebp, [PID+6] ; LEA PID Buffer 6byte in    
    jmp ASCIIConv    ;Run the loop to convert int to string
ASCIIConv:
[/code]

<p>Above the divisor is set to 10 (0xA) and moved to the EBX register, this will be the operand that is passed to DIV. The address of the buffer is then stored into the EBP register, the reason for the "+6" will become apparent shortly. The last step is to jump to the ASCII conversion loop.</p>

[code]
start:
    mov eax, 0x14                     ; GET_PID call
    int 0x80                          ; Call
    mov ebx, 0xA                      ; Set divider to 10
    lea ebp, [PID+6]                  ; LEA PID Buffer 6byte in
    jmp ASCIIConv                     ; Run the loop to convert int to string
ASCIIConv:
    div ebx                           ; Divide the PID
    mov cl, [LookUpDig+edx]           ; Copy ASCII value to CL
    mov [ebp], cl                     ; Copy CL to PID var
    dec ebp                           ; Next byte into buffer
    xor edx, edx                      ; Clear the remainder
    inc eax                           ; Dec eax tricking jnz
    dec eax                           ; Push back to original value
    jnz ASCIIConv                     ; Keep looping until eax is zero
    jz .printOut                      ; Print out the buffer
[/code]

<p>Above is the main loop of the program, it takes the integer returned from <em>get_pid</em> and converts it to a string. The first instruction divides EAX by EBX, remember that 10 was stored into EBX so the division looks like this: EAX/10. When the division is completed the remainder (What I need) is stored in the EDX register. I will use the number in EDX (e.g. 4) to index into the look-up table thus returning the ASCII code for the number, the code is then stored into CL.</p>

<p>The code is then moved to the buffer defined in the <em>.bss</em> section. Remember that it's actually being moved 6bytes into the buffer, this is because the number is being built backwards - hopefully a diagram will help visualize this.</p>

<pre>
PID Number             +1 +2 +3 +4 +5 +6 +7 +8
<hr />
500213/10 = 50021.3  → [ ][ ][ ][ ][ ][3][ ][ ]
50021/10 = 5002.1    → [ ][ ][ ][ ][1][3][ ][ ]
5002/10 = 500.2      → [ ][ ][ ][2][1][3][ ][ ]
500/10 = 50.0        → [ ][ ][0][2][1][3][ ][ ]
50/10 = 5.0          → [ ][0][0][2][1][3][ ][ ]
5/10 = 0.5           → [5][0][0][2][1][3][ ][ ]
</pre>

<p>Now when the buffer is passed to <em>sys_write</em> the number will be printed left to right, exactly how it should be. After moving the ASCII code to the buffer the pointer needs to be decreased by 1 byte (See above diagram). Next the remainder is cleared from the EDX register, if not cleared there are bizarre results when the DIV runs for the second time.</p>

<p>Unfortunately clearing EDX sets the zero flag (ZF) which is a problem since I use the ZF to check when EAX is zero, meaning the conversion is done. As a solution I came up with a little hack, increase EAX by one which resets the ZF and then decrease it back to its original value. This works perfectly since when EAX is decreased back to the original value if it hits zero, then the flag is set again meaning the loop is done since EAX is 0.</p>

<p>When EAX hits zero the conversion is done and the loop can be exited, this is checked with the JNZ command. Once the loop is exited, the buffer will contain the same number returned from <em>get_pid</em> but as a string meaning it can now be printed to the console.</p>

[code]
SECTION .data
    PIDString db "PID: "
    PIDLength equ $-PIDString
[/code]

<p>The above data is used as a text label to be printed to the console, if this was printed to the console on its own it would look like: </p>

<blockquote>
www:getpid theropfather$ PID:
</blockquote>

[code]
.printOut:
    push PIDLength                    ; Push PIDString Length
    push PIDString                    ; Push PIDString
    push 0x1                          ; FD stdout
    mov eax, 0x4                      ; sys_write call
    push eax                          ; Push call (BSD)
    int 0x80                          ; Call
    add esp, 0x10                     ; Clear up the stack

    mov [PID+7], byte 0xA             ; Push a newline to PID string
    push 0x8                          ; Max length of 8 bytes
    push PID                          ; Push PID value
    push 0x1                          ; FD stdout
    mov eax, 0x4                      ; sys_write call
    push eax                          ; Push call (BSD)
    int 0x80                          ; Call
    add esp, 0x10                     ; Clean up stack

    mov eax, 0x1                      ; Set system_call
    push 0x0                          ; Exit_code 0
    int 0x80                          ; Call
[/code]

<p>This is the last section of code, although it looks long it is really rather simple. I call two system writes, the first is to write the text label and the second to write the converted integer. If you come from Linux assembly the above calling convention may look a a little odd, this is because it's the FreeBSD calling convention. You can read a <a href="http://www.freebsd.org/doc/en/books/developers-handbook/x86-system-calls.html">full description here</a>. Basically the string length is pushed to the stack, then the address of the string to be printed. The file descriptor is the last argument passed to system write (I'm using FD1 = stdout). Finally the call number is moved to EAX and EAX is pushed to the stack, pushing the call number alone will <strong>not</strong> work, it has to be placed into the EAX register first.</p>

<p>The stack is cleaned up after each call and finally the last three lines call system exit with an exit code of 0 (All went well).</p>

Here is the final application:

[code]
;**************************************************************
; Author  : TheROPFather                                      *
; Date    : 02/04/2013                                        *
; Contact : theropfather@gmail.com | twitter.com/theropfather *
; Compile : nasm -f macho -o pid.o getpid.asm                 *
; Link    : ld -macosx_version_min 10.7 -o pid pid.o          *          
; Descr.  : Prints the process PID, could be used in a        *
;           larger program.                                   *
; Nasm v. : NASM version 0.98.40                              * 
;**************************************************************

SECTION .data

LookUpDig db "0123456789"                 ; Lookup for ASCII

PIDString db "PID: "
PIDLength equ $-PIDString

SECTION .bss

PID: resb 8                               ; Reserve space for result

SECTION .text

global start

start:
	mov eax, 0x14                     ; GET_PID call
	int 0x80                          ; Call  
	mov ebx, 0xA                      ; Set divider to 10
	lea ebp, [PID+6]                  ; LEA PID Buffer 6byte in
	jmp ASCIIConv                     ; Run the loop to convert int to string
ASCIIConv: 
	div ebx                           ; Divide the PID
	mov byte cl, [LookUpDig+edx]	  ; Copy ASCII value to CL
	mov [ebp], cl                     ; Copy ASCII value to buffer
	dec ebp                           ; Next byte into buffer
	xor edx, edx                      ; Clear the remainder
	inc eax                           ; Dec eax tricking jnz
	dec eax                           ; Push back to original value
	jnz ASCIIConv                     ; Keep looping until eax is zero
	jz .printOut                       ; Print out the buffer
.printOut:
	push PIDLength                    ; Push PIDString Length
	push PIDString                    ; Push PIDString
	push 0x1                          ; FD stdout
	mov eax, 0x4                      ; sys_write call
	push eax                          ; Push call (BSD)
	int 0x80                          ; Call
	add esp, 0x10                     ; Clear up the stack
	
	mov [PID+7], byte 0xA             ; Push a newline to PID string
	
	push 0x8                          ; Max length of 8 bytes
	push PID                          ; Push PID value
	push 0x1                          ; FD stdout
	mov eax, 0x4                      ; sys_write call
	push eax                          ; Push call (BSD)
	int 0x80                          ; Call
	add esp, 0x10                     ; Clean up stack

	mov eax, 0x1                      ; Set system_call
	push 0x0                          ; Exit_code 0
	int 0x80                          ; Call
[/code]

So that's it, my experience writing a (simple?) ASM application this can now be transformed into a library for use in future applications. If you have any questions or improvements feel free to contact me on twitter, or in the comments down below :)

Twitter: <a href="http://www.twitter.com/theropfather">theROPfather</a>

--TheROPFather  


You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
