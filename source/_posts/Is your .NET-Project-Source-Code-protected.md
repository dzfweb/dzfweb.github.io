---
title: Is your .NET Project Source Code protected?
date: 2013-12-01 15:07:04
tags: 
- security 
- .NET
---
Probably you Developer must have asked yourself: “Is my code protected?”. I came across this question at the end of a system that I had developed in .NET and was about to put it into production, and like any cautious developer, I started researching methodologies to protect my System and, consequently, the System's source code.

<!--more-->


Well, this study brought me an insecurity about the .NET Technology, which for me until then, was considered very safe. Amazingly, it is possible to extract the source code of any .NET project (C#, VB, etc). In this article I will share with you how it is possible to do this and how to protect your code and make this practice a little more difficult.

## Understanding .NET DLLs and Executables

In summary, when you compile a code in .NET, it is not converted to machine language. All .NET languages are converted to MSIL (Microsoft Intermediate Language) . MSIL is converted at runtime. This process offers some advantages such as dynamically querying Assemblies and their methods. However, thanks to this process, it is also possible, through reverse engineering, to access the entire algorithm of the code, its methods, variables, etc.

## .NET decompilers

Microsoft has a kind of MSIL Decompiler where it is possible to access the methods of any .NET assembly (Learn more by clicking here) however, there are some more used decompilers that offer some functions in addition, such as extracting all the source code of the Assembly, including the .CSPROJ.

I'm talking about Reflector (download here) and File Disassembler plugin (download here). You'll see below how the two together can extract the source code of DLLs or .NET executables.

This is the Reflector Interface. As soon as you open the program, it loads the Microsoft Assembly by default, where you can browse the methods. Until then, in amazement, because with Visual Studio it is already possible to do this.

Reflector

I created a new very simple Windows Form C# project to use in the example. It has 1 Form and a method. In the Reflector > Open Assembly program I selected the executable I made, and then the program decodes the MSIL of the program and displays the methods.

Reflector

Now I'm going to use the Plugin File Disassembler by selecting it in the menu Tools > File Disassembler, in the window that opens I inform the type of Project as Windows Form and the path for the output of the decompiled files. The Plugin then extracts all source code from the selected Assembly.

Reflector

And then after this simple process and with access to the assembly source code, I can open it in Visual Studio without any problem.

Reflector

Were you amazed? Yeah, me too. And because of that I left for another search.

## How to Protect .NET Codes with Dotfuscator

As of the 2012 version of Visual Studio, the Dotfuscator extension from the company PreEmptive is installed by default and can be accessed in Tools >** PreEmptive Dotfuscator and Analytics **.

1- When opening the DotFuscator Interface, start a new project File > New Project.

2- Click on Inputs and next to it, add the Assembly or Executables that you want to obfuscate.
(Ex: ..ProjetoBinMeuProjeto.exe)

3- Click on Build > Build Project. This process will create a folder in your selected Project directory with the name Dotfuscated and the Executable or DLL with MSIL obfuscated.

 

1
Okay, your code has been protected and it is not possible to decompile. Now all you have to do is distribute the obfuscated executable to your clients. If you try to do this in Reflector, the result will be an encrypted and unreadable file.