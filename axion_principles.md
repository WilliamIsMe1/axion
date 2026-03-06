## Syntax

1. This is like Java in basic syntax except for semicolons. There are none. All statements must be on separate lines. Curly braces are used, declarations are `returntype name(type param) {}` or `type name = value`
2. Keywords are not shortened to impl or func (func doesn't even exist, as we use returntype in it's place to indicate a function) and print is not a keyword. Keywords are carefully chosen before creation so as to prevent awkward problems, such as a function called assert being replaced by a keyword.
3. Very little punctuation mashing, but not bordering on a full NLP needed for a parser. Puncuation is used when it makes sense, like periods, unary operators, binary operators, things like that. Examples may include nullability operators, exponentiation, simple arithmetic or bitwise logic
4. The standard library doesn't get special syntax treatment. One must be able to rebuild it from scratch (so no Object superclasses everything) using just the source files. VERY FEW EXCEPTIONS.
5. Certain unusual operators exist, such as <=> or ?. or ?? or perhaps even ** for exponentiation. Operator overloading is achieved through special methods like .add() or .sub(), or .cmp() for <=>

## Style conventions

1. camelCase for local and global variables and parameters
2. PascalCase for classes, interface names, fields, methods, and global functions.
3. lowercase for struct names and namespaces.
4. Opening { on same line cause the other convention is a waste of screen space

## Principles

1. FP and OOP exist together and have an untouched synergy.
2. Not everything is an object.
3. Strings are primitives and arrays are treated as primitives. If it has a literal it is a primitive
4. Efficiency and convenience must be balanced. Value classes are an example.
5. One should not need a roundabout workaround solution for any problems.
6. Strict strict typing, with optional workarounds.
7. Bytecode portability with native interoperability. The language cannot become a walled garden.
8. Standard library cannot be babied. If I can do it, you should be able to as well. This means one should be able to rewrite the entire standard library from scratch.
9. Very horizontal standard library. Networking? There. Nonblocking file io? There. Threading? there. OpenGL? there. Windowing? Sure. quantum resistant cryptography? Two steps ahead of you. Crypto mining? You do that yourself I ain't wasting my time but sure I'll provide some of the tools. 
10. Abstraction from the OS choice, but the system must be accessible.
11. Fast reflection and fast sandboxed behavior. Security should not be compromised.
12. Dynamic languages should be able to compile to the VM's bytecode and work.
13. Freedom. Annotating a block with things like @NonStrictMath or @NoGC should allow a performance boost in critical loops. Type safety for speed is not a good trade, so I want to allow both.

## Annotations

Annotations are interesting in that they are able to modify compiler behavior without giving special privilages to the standard library. Annotations can apply to variable declarations, methods, functions, classes, structs, interfaces, mixins, and even code blocks.

Example:
```
@UnsafeMath(default = 0) {
	float sum = 0.0f
	int counter = 0
	for (float f : listOfNumbers) {
		sum += f
		counter++
	}
	float avg = sum / counter // This sometimes yields n / 0, so we turn that into Infinity or zero, and since here the default is 0, we yield zero for uncertain cases
}
```

## Memory model

The memory model is quite interesting, as it is made to allow the benefits of manual memory management and the beginner friendly ease of use of a garbage collector. An object is constructed in two ways, depending on if it was based off of a class or a struct. Classes are reference types, and structs are value types. Any reference to a class object is actually a pointer, and must be freed. Structs, arrays, and primitives are non allocated by default and stored on the stack. The compiler generates free calls whenever it can, and whenever it can't, it puts things on a separate garbage collected heap. @NoGC and other such annotations automatically force everything onto the non-garbage collected heap.

In other words, you can leave it up to the GC or allocate and free memory yourself. Both work and both can be used.

## Compilation

To compile, one must use the `axion` command, specifically the compile subcommand. This compiles the files into a .axc file, which can be run using `axion run <file>`. This can also run archives of .axc files from .aar, which is stored in a similar format to .jar or .zip. A .axc file is in essence one module, full of classes and constants and other things.

## The standard library

One of the main principles of this language is that the standard library has zero special permissions, so you don't need the std.thread class to create threads. You could theoretically build up your own standard library from scratch. The way that certain bytecode things would be achieved is through the bytecode operator, which takes a string literal as bytecode assembly and translates it directly into bytecode.
```
bytecode("""
ldfunc #13 ; axion.lang:println(string)
""") // The signature is void println(string in)
```

This means that you could call bytecode such as thread_spawn or thread_join, so threads could be handled via runtime. Due to native library support, anyone could create those functions themselves, pack them into a .dll or .dylib or .so and attach it at runtime. Only primitives such as char, int, long, float, double, short, string, bool, or void and null can live without a standard library.

The standard library implemented will be extremely horizontal in construction, 
