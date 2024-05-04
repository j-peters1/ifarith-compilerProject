# Discussion and Reflection


The bulk of this project consists of a collection of five
questions. You are to answer these questions after spending some
amount of time looking over the code together to gather answers for
your questions. Try to seriously dig into the project together--it is
of course understood that you may not grasp every detail, but put
forth serious effort to spend several hours reading and discussing the
code, along with anything you have taken from it.

Questions will largely be graded on completion and maturity, but the
instructors do reserve the right to take off for technical
inaccuracies (i.e., if you say something factually wrong).

Each of these (six, five main followed by last) questions should take
roughly at least a paragraph or two. Try to aim for between 1-500
words per question. You may divide up the work, but each of you should
collectively read and agree to each other's answers.

[ Question 1 ] 

For this task, you will three new .irv programs. These are
`ir-virtual?` programs in a pseudo-assembly format. Try to compile the
program. Here, you should briefly explain the purpose of ir-virtual,
especially how it is different than x86: what are the pros and cons of
using ir-virtual as a representation? You can get the compiler to to
compile ir-virtual files like so: 

racket compiler.rkt -v test-programs/sum1.irv 

(Also pass in -m for Mac)

### Q1 Answer:
The purpose of ir-virtual is to provide a way to create programs that are very similar to ASM programs, but easier to understand and 
create. This is a pro because it allows people to create and compile programs into ASM from a more friendly language and without having to directly code using ASM. The structure and syntax of ir-virtual? is also very simple, and when creating test programs we found it easy to follow the definitions and create different test cases. When writing ASM, there are alot more things to take into a account when creating a program but when using ir-virtual you only have to worry about the instructions. A negative to this is that bugs may occur and there may be issues that arise when translating ir-virtual to ASM, as you have to rely on a program to interpret the ir-virtual code and there could be problems. We actually ran into some issues compiling some ir-virtual programs, which may be caused by the translator. Writing directly using ASM would negate any potential error that could come from translating. 


[ Question 2 ] 

For this task, you will write three new .ifa programs. Your programs
must be correct, in the sense that they are valid. There are a set of
starter programs in the test-programs directory now. Your job is to
create three new `.ifa` programs and compile and run each of them. It
is very much possible that the compiler will be broken: part of your
exercise is identifying if you can find any possible bugs in the
compiler.

For each of your exercises, write here what the input and output was
from the compiler. Read through each of the phases, by passing in the
`-v` flag to the compiler. For at least one of the programs, explain
carefully the relevance of each of the intermediate representations.

For this question, please add your `.ifa` programs either (a) here or
(b) to the repo and write where they are in this file.

### Q2 Answer:

test1ifa.ifa (in git repo, test-programs folder)  
Input: (let* ([x 5] [y 10]) (print (+ x y)))  

Output:   
Input source tree in IfArith:  
'(let* ((x 5) (y 10)) (print (+ x y)))  
ifarith-tiny:  
'(let ((x 5)) (let ((y 10)) (print (+ x y))))  
'(let ((x 5)) (let ((y 10)) (print (+ x y))))  
5  
'(let ((y 10)) (print (+ x y)))  
10  
'(print (+ x y))  
'(+ x y)  
'x  
'y  
anf:  
'(let ((x1254 5))  
   (let ((x x1254))  
     (let ((x1255 10))  
       (let ((y x1255)) (let ((x1256 (+ x y))) (print x1256))))))  
ir-virtual:  
'(((label lab1257) (mov-lit x1254 5))  
  ((label lab1258) (mov-reg x x1254))  
  ((label lab1259) (mov-lit x1255 10))  
  ((label lab1260) (mov-reg y x1255))  
  ((label lab1261) (mov-reg x1256 x))  
  (add x1256 y)  
  ((label lab1262) (print x1256))  
  (return 0))  
x86:  
section .data  
	int_format db "%ld",10,0  
	global _main  
	extern _printf  
section .text  
_start:	call _main  
	mov rax, 60  
	xor rdi, rdi  
	syscall  
_main:	push rbp  
	mov rbp, rsp  
	sub rsp, 80  
	mov esi, 5  
	mov [rbp-8], esi  
	mov esi, [rbp-8]  
	mov [rbp-16], esi  
	mov esi, 10  
	mov [rbp-32], esi  
	mov esi, [rbp-32]  
	mov [rbp-40], esi  
	mov esi, [rbp-16]  
	mov [rbp-24], esi  
	mov edi, [rbp-40]  
	mov eax, [rbp-24]  
	add eax, edi  
	mov [rbp-24], eax  
	mov esi, [rbp-24]  
	lea rdi, [rel int_format]  
	mov eax, 0  
	call _printf  
	mov rax, 0  
	jmp finish_up  
finish_up:	add rsp, 80  
	leave   
	ret  

Explanation:
The phases depict each of the steps in the Compiler process of turning an ifa file into an assembly file. The first step shows how the machine reads the input file in Ifarith. It then converts Ifarith onto Ifarith->tiny. During this process,  it goes through the input, evaluating it, and printing out the values of the variables bound by let. At the end, all the variables used/have a value assigned to them are printed out. Then it converts the ifarith->tiny code into the anf code in order to simplify it using bindings so that operations are done one at a time. The output is printed. Then, the compiler converts the anf code into ir-virtual code. The purpose of this conversion is to change the series of lets from the ANF code into a series of simple instructions carried out one at a time, preparing it for the final conversion. The conversion of ir-virtual code to Assembly code is the final step where the compiler performs a relatively simple conversion as ir-virtual is inherently similar to the assembly code format. 


test2ifa.ifa (in git repo, test-programs folder)  
Input: (let* ([x 1] [y 15]) (let* ([z 10]) (+ (+ x y) z)))  
  
Output:   
Input source tree in IfArith:  
'(let* ((x 1) (y 15)) (let* ((z 10)) (+ (+ x y) z)))  
ifarith-tiny:  
'(let ((x 1)) (let ((y 15)) (let ((z 10)) (+ (+ x y) z))))  
'(let ((x 1)) (let ((y 15)) (let ((z 10)) (+ (+ x y) z))))  
1  
'(let ((y 15)) (let ((z 10)) (+ (+ x y) z)))  
15  
'(let ((z 10)) (+ (+ x y) z))  
10  
'(+ (+ x y) z)  
'(+ x y)  
'x  
'y  
'z  
anf:  
'(let ((x1254 1))  
   (let ((x x1254))  
     (let ((x1255 15))  
       (let ((y x1255))  
         (let ((x1256 10))  
           (let ((z x1256))  
             (let ((x1257 (+ x y))) (let ((x1258 (+ x1257 z))) x1258))))))))  
ir-virtual:  
'(((label lab1259) (mov-lit x1254 1))  
  ((label lab1260) (mov-reg x x1254))  
  ((label lab1261) (mov-lit x1255 15))  
  ((label lab1262) (mov-reg y x1255))  
  ((label lab1263) (mov-lit x1256 10))  
  ((label lab1264) (mov-reg z x1256))  
  ((label lab1265) (mov-reg x1257 x))  
  (add x1257 y)  
  ((label lab1266) (mov-reg x1258 x1257))  
  (add x1258 z)  
  (return x1258))  
x86:  
section .data  
	int_format db "%ld",10,0  
	global _main  
	extern _printf  
section .text  
_start:	call _main  
	mov rax, 60  
	xor rdi, rdi  
	syscall  
_main:	push rbp  
	mov rbp, rsp  
	sub rsp, 128  
	mov esi, 1  
	mov [rbp-32], esi  
	mov esi, [rbp-32]  
	mov [rbp-40], esi  
	mov esi, 15  
	mov [rbp-24], esi  
	mov esi, [rbp-24]  
	mov [rbp-48], esi  
	mov esi, 10  
	mov [rbp-16], esi  
	mov esi, [rbp-16]  
	mov [rbp-56], esi  
	mov esi, [rbp-40]  
	mov [rbp-8], esi  
	mov edi, [rbp-48]  
	mov eax, [rbp-8]  
	add eax, edi  
	mov [rbp-8], eax  
	mov esi, [rbp-8]  
	mov [rbp-64], esi  
	mov edi, [rbp-56]  
	mov eax, [rbp-64]  
	add eax, edi  
	mov [rbp-64], eax  
	mov rax, [rbp-64]  
	jmp finish_up  
finish_up:	add rsp, 128  
	leave   
	ret   



test3ifa.ifa (in git repo, test-programs folder)  
Input: '(let* ((x 10) (z 5) (y 9)) (let* ((b (* x z))) (- b y)))  
  
Output:  
Input source tree in IfArith:  
'(let* ((x 10) (z 5) (y 9)) (let* ((b (* x z))) (- b y)))  
ifarith-tiny:  
'(let ((x 10)) (let ((z 5)) (let ((y 9)) (let ((b (* x z))) (- b y)))))  
'(let ((x 10)) (let ((z 5)) (let ((y 9)) (let ((b (* x z))) (- b y)))))  
10  
'(let ((z 5)) (let ((y 9)) (let ((b (* x z))) (- b y))))  
5  
'(let ((y 9)) (let ((b (* x z))) (- b y)))  
9  
'(let ((b (* x z))) (- b y))  
'(* x z)  
'x  
'z  
'(- b y)  
'b  
'y  
anf:  
'(let ((x1254 10))  
   (let ((x x1254))  
     (let ((x1255 5))  
       (let ((z x1255))  
         (let ((x1256 9))  
           (let ((y x1256))  
             (let ((x1257 (* x z)))  
               (let ((b x1257)) (let ((x1258 (- b y))) x1258)))))))))  
ir-virtual:  
'(((label lab1259) (mov-lit x1254 10))  
  ((label lab1260) (mov-reg x x1254))  
  ((label lab1261) (mov-lit x1255 5))  
  ((label lab1262) (mov-reg z x1255))  
  ((label lab1263) (mov-lit x1256 9))  
  ((label lab1264) (mov-reg y x1256))  
  ((label lab1265) (mov-reg x1257 x))  
  (imul x1257 z)  
  ((label lab1266) (mov-reg b x1257))  
  ((label lab1267) (mov-reg x1258 b))  
  (sub x1258 y)  
  (return x1258))  
x86:  
section .data  
	int_format db "%ld",10,0  
	global _main  
	extern _printf  
section .text  
_start:	call _main  
	mov rax, 60  
	xor rdi, rdi  
	syscall  
_main:	push rbp  
	mov rbp, rsp  
	sub rsp, 144  
	mov esi, 10  
	mov [rbp-32], esi  
	mov esi, [rbp-32]  
	mov [rbp-40], esi  
	mov esi, 5  
	mov [rbp-24], esi  
	mov esi, [rbp-24]  
	mov [rbp-56], esi  
	mov esi, 9  
	mov [rbp-16], esi  
	mov esi, [rbp-16]  
	mov [rbp-48], esi  
	mov esi, [rbp-40]  
	mov [rbp-8], esi  
	mov edi, [rbp-56]  
	mov eax, [rbp-8]  
	imul eax, edi  
	mov [rbp-8], eax  
	mov esi, [rbp-8]  
	mov [rbp-64], esi  
	mov esi, [rbp-64]  
	mov [rbp-72], esi  
	mov edi, [rbp-48]  
	mov eax, [rbp-72]  
	sub eax, edi  
	mov [rbp-72], eax  
	mov rax, [rbp-72]  
	jmp finish_up  
finish_up:	add rsp, 144  
	leave   
	ret   




[ Question 3 ] 

Describe each of the passes of the compiler in a slight degree of
detail, using specific examples to discuss what each pass does. The
compiler is designed in series of layers, with each higher-level IR
desugaring to a lower-level IR until ultimately arriving at x86-64
assembler. Do you think there are any redundant passes? Do you think
there could be more?

In answering this question, you must use specific examples that you
got from running the compiler and generating an output.

### Q3 Answer:

When translating code, the compiler first starts at the level if IfArith. The main difference between ifArith and a higher-level language is that ifArith has special handling for false, as false is equivalent to 0, and true is anything except 0. When the compiler translates from ifArith to the next stage, ifArith-Tiny removes some aspects of the language and replaces them with a simpler implementation. For example, when inputting a let* function into the compiler, ifArith-tiny takes the let* and translates it into a series of lets, removing the need to have let* implemented in the ifArith-tiny language. The next step, Administrative Normal Form, takes the ifArith-tiny language and translates it to a series of single-operation lets, as assembly only has the ability to execute operations once at a time. The compiler then translates ANF into ir-Virtual, which is a virtual representation of ASM with the instructions used. 

I don’t think there are any redundant passes, as each pass seems to do a specific job, but possibly from ir-virtual to ASM, as it appears that ir-virtual just makes it a bit easier to write and understand the code, but can raise issues, however, it is still useful. I don’t think there is a need for any more passes. 



[ Question 4 ] 

This is a larger project, compared to our previous projects. This
project uses a large combination of idioms: tail recursion, folds,
etc.. Discuss a few programming idioms that you can identify in the
project that we discussed in class this semester. There is no specific
definition of what an idiom is: think carefully about whether you see
any pattern in this code that resonates with you from earlier in the
semester.

### Q4 Answer:

One of the most prevalent idioms/similarities we noticed in this project and in most previous projects, is the usage of a match statement and pattern matching. Pattern matching has become essential when creating these interpreter/translator style functions, and we see it again here in the compiler as every level of the compiler uses pattern matching to match different expressions and cases. This is also combined with recursion, as almost every time the output of a matched case is called back through the same function to further simplify and interpret/translate the expression. Higher-order functions are also common idioms, like foldl/r and map, which we can see fold is used in the compiler to translate ir-virtual to x86. We have also used these functions a lot in the past on previous projects and they are extremely useful. 

[ Question 5 ] 

In this question, you will play the role of bug finder. I would like
you to be creative, adversarial, and exploratory. Spend an hour or two
looking throughout the code and try to break it. Try to see if you can
identify a buggy program: a program that should work, but does
not. This could either be that the compiler crashes, or it could be
that it produces code which will not assemble. Last, even if the code
assembles and links, its behavior could be incorrect.

To answer this question, I want you to summarize your discussion,
experiences, and findings by adversarily breaking the compiler. If
there is something you think should work (but does not), feel free to
ask me.

Your team will receive a small bonus for being the first team to
report a unique bug (unique determined by me).


### Q5 Answer:

The first bug we found was when trying to compile an ir-virtual? program that used mul as one of the instructions. When creating simple test programs, like   
((mov-lit r0 5)  
(mov-lit r1 6)  
(mul r1 r0)  
(print r1))  
for example, the compiler compiles, but when trying to assemble the program it gives an error and does not assemble.   
jackpeters@Jacks-MacBook-Pro ifarith-compilerProject % nasm -fmacho64 test-programs/testmult.asm  
test-programs/testmult.asm:25: error: invalid combination of opcode and operands  

The error given is an invalid combination of opcode and operands. When trying to run this exact same test file but with (mul r1 r0)  
 replaced with (add r1 r0), the program compiles, assembles, and links correctly.  

We also found that this same issue occurs when trying to use (idiv r1 r0) as well, and produces the same error.   

Another issue we discovered was surrounding booleans, specifically literal booleans like #t and #f. Some given test programs, like  bool0.ifa and bool1.ifa do not compile as there is a missing match case for literal booleans. We believe that this issue comes from a missing match case, possibly from an incorrect implementation in the compiler program at some level of the language. 

Our process included just testing lots of different input programs at different levels of the compiler, and trying to see if we could get a broken output or error produced.


[ High Level Reflection ] 

In roughly 100-500 words, write a summary of your findings in working
on this project: what did you learn, what did you find interesting,
what did you find challenging? As you progress in your career, it will
be increasingly important to have technical conversations about the
nuts and bolts of code, try to use this experience as a way to think
about how you would approach doing group code critique. What would you
do differently next time, what did you learn?


### Reflection Answers:

#### Jack:
This project was super interesting and really expanded my knowledge about not just the x86 ASM language, but the process of translating code step-by-step into ASM. Even though we only implemented a small portion of the compiler, I still feel that I learned a lot by reading through the compiler and looking at all the other parts of it, especially the description of each step. This really helped me to understand exactly how the compiler went from step to step, and which precise translations and aspects of the code gets changed on each level. Writing varying test programs was also interesting, as the output of the compiler provided good information about each level for some specific test input. While this all made sense in the end, understanding exactly how the compiler worked and what the format for different levels were was the most challenging part for me in the project. It took awhile to actually figure out what the goal was and how to run a program, as a lot of this was new to me. Next time, I would definitely focus more on just reading through the compiler and understanding the project before trying to write any code or answer any questions, just to make sure I had a solid understanding of everything. 

#### Philippe:
While I already had some basic understanding of the ASM language, my knowledge on the processes of compilation expanded, as I had no idea what happened in between the original file and the ASM output. Having to go and see the code used for the conversion line by line was interesting. I especially like reading through the output produced when completing question 2. I spent many minutes simply looking at the code, line by line, just trying to understand every conversion made in the compiling process. The most challenging thing was trying to debug and trying to fix said bugs. Testing the code might produce the correct output but when trying to compile files that would normally use daid code would produce an error. If I were to do this differently I would spend more time digging through the code to understand and fix the bugs we had found. While we did attempt to understand and fix them, we could not ultimately fix the bugs.


