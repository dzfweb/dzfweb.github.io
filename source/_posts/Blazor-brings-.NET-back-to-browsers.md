---
title: Blazor brings .NET back to browsers
date: 2017-09-10 15:08:52
tags: 
- blazor
- .net
---

Blazor is an experiment created by Steve Sanderson that makes .NET possible for browsers using WebAssembly and DotNetAnywhere. It is not a complete Framework like Flash or Silverlight, but a test to see what is possible to do with such technology.
<!--more-->


WebAssembly describes itself as a standard for "a secure, testable, and even deployable JavaScript virtual machine execution environment." Although it performs better in browsers that support it natively, WebAssembly can be interpreted by older browsers through converters and asm.js.

Currently, WebAssembly is designed to support applications written in C and C++, acting as a backend compiler for clang/LLVM. To convert C into C#, Blazor uses DotNetAnywhere, created by Chris Bacon, which is nothing more than an MSIL interpreter for the .NET Framework. The DotNetAnywhere project ended about 6 years ago.

Blazor makes the following changes to DotNetAnywhere:

- Development support with Emscripten
- Support for JavaScript calls through .NET (and vice versa)
- Adds primitive interop features (eg GCGandle)
- Fixes some bugs
- Support for loading .NET Core-style resources

Applications with Blazor are built using Razor templates, which run in the browser rather than on the server. According to the documentation, a simple “Hello world” requires a 300KB download. This includes everything needed, namely:

- small and lightweight .NET runtime
- Core libs
- Application code
- Encapsulated codes needed to run the WebAssembly code

No effort has been made to remove unnecessary code, so it's still possible to reduce the size further.

To see Blazor in action, watch the video of the presentation given at the NDC conference “Web Apps can’t really do that, can they? - Steve Sanderson” (Web apps really can't do that, can they?).

Artigo originalmente publicado no InfoQ
https://www.infoq.com/br/news/2017/09/Blazor