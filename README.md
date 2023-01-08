# Arch Lab

## Introduction

ICS Arch Lab, Peking University.

In this lab, you will learn about the design and implementation of a pipelined Y86-64 processor, optimizing both it and a benchmark program to maximize performance. You are allowed to make any semantics preserving transformation to the benchmark program, or to make enhancements to the pipelined processor, or both. When you have completed the lab, you will have a keen appreciation for the interactions between code and hardware that affect the performance of your programs.

The lab is organized into three parts, each with its own handin. In Part A you will write some simple Y86-64 programs and become familiar with the Y86-64 tools. In Part B, you will extend the SEQ simulator with a new instruction. These two parts will prepare you for Part C, the heart of the lab, where you will optimize the Y86-64 benchmark program and the processor design.

For more information about this lab, please refer to ```archlab.pdf```.

## Installation

You can do the lab on Linux and Mac systems. To build the Y86-64 tools, you need install the following libraries: ```flex```, ```bison```. On Ubuntu systems, you can use the command:
```
sudo apt install flex bison
```

If you want to use GUI mode to test your solution, you should install ```tcl```, ```tcl-dev```, ```tk```, ```tk-dev``` in addition. But we do not guarantee the compatibility of the libraries. If you are using MacOS, ```flex``` and ```bison``` are supported inherently but GUI mode may not be supported.

## Score

My score for this lab is as follows.
| Total score | Part C Average CPE |
| ----------- | ------------------ |
| 130.0       | 4.33               |

It is possible to make the part C average CPE even lower by using loop unrolling, which I have not tried.

Notably, by using some black magic, we can achieve an average CPE of 0.59 in part C. I have provided the codes in ```ncopy0.59.ys``` and ```pipe-full0.59.hcl``` in the ```archlab-handout/sim/pipe``` directory, but I will not explain them in details.

## Principle

The core of my implementation of part C is the following three lines of code in ```ncopy.ys```:
```
iaddq $7, %rdi      # use forwarding to get src, move (src) to %r10 and src++
iaddq $1, %rax      # use forwarding to get %r10 and if 0 < R[%r10] < 300, count++; also move %r10 to (dst) if R[%r10] < 300
iaddq $8, %rsi      # dst++; if R[%r10] < 300, goto Loop
```

The three tables below describe what each instruction does in different stages of the pipeline processor.

![Stages of ```iaddq $7, %rdi```.](https://github.com/GeorgeMLP/archlab/raw/master/Figures/fig1.png)

![Stages of ```iaddq $1, %rax```.](https://github.com/GeorgeMLP/archlab/raw/master/Figures/fig2.png)

![Stages of ```iaddq $8, %rsi```.](https://github.com/GeorgeMLP/archlab/raw/master/Figures/fig3.png)

You can refer to the header comments in ```ncopy.ys``` and ```pipe-full.hcl``` for more details.
