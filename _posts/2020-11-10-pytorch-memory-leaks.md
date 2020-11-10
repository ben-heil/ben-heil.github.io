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

## Memory Leak Examples

## TorchSeal
