---                                                                                                                                                                                                         
layout: post                                                                                                                                                                                                
title: How to Find and Fix Pytorch Memory Leaks
description: Detecting common causes of the "CUDA out of memory" error
cover-img: "/assets/img/lakeview.jpg"                                                                                                                                                                    
---

# Introduction
Pytorch is an extremely flexible deep learning framework, but that flexibility can lead to issues.
Where other frameworks abstract away the physical location of memory and which tensors are being optimized, Pytorch requires you to be explicit.
As a result, there is a class of bug that arises in Pytorch code that doesn't arise in any other Python code I know of: memory leaks.

This article explains what a memory leak is, shows a few examples of memory leaks, and demos a tool I wrote to debug them more easily.

## What is a Memory Leak
Memory leaks are a type of bug where variables' memory isn't deallocated after use.
These types of bugs are typically found in languages that use [manual memory management](https://developer.ibm.com/technologies/systems/articles/au-toughgame/) 
instead of [garbage collection](https://docs.python.org/3/library/gc.html).
Python's garbage collector works well in most cases, which is why you don't normally have to think about memory leaks when writing Python code.
However, some [common mistakes](#examples) in writing Pytorch code can confuse the garbage collector and prevent memory from being deallocated.

Memory leaks are typically silent, which is to say that they don't raise exception on their own.
Instead they cause increased memory usage, potentially leading to crashes in when memory runs out.
If a program runs on memory-constrained hardware (like a GPU), memory leaks often go from silent errors to full crashes.
Memory leaks can fly under the radar by leaking small amounts of memory each epoch so that they don't lead to errors in unit tests.
To make them easier to correct, [I wrote a tool](#torchseal) that watches for memory leaks and prints diagnostic information if one is found.

<a id='examples'></a>
## Memory Leak Examples
### Accumulating loss

### Creating a list of tensors (instead of floats)

### Creating neural nets in a subclass

<a id='torchseal'></a>
## TorchSeal
