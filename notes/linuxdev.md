### Create object files for a static library

```gcc -c src/tq84/add.c -o bin/static/add.o```

### Create object files for a dynamic library

```gcc -c -fPIC src/tq84/add.c -o bin/shared/add.o```

Object files for the shared library need to be compiled with the -fPIC flag (PIC = position independent code). 

### Create a static library

```ar rcs bin/static/libtq84.a bin/static/add.o bin/static/answer.o```

A static library is basically a set of object files that were copied into a single file with the suffix .a.
The static file is created with the archiver ( ar).

### Link statically

```gcc bin/main.o -Lbin/static -ltq84 -o bin/statically-linked```

The -L flag indicates (non standard) directory where the libraries can be found. The -l flag indicates the name of 
the library. Note, that it assumes the library to start with lib and end with .o (so lib and .o must not be 
specified). The created executable bin/statically-linked is not dependend on any other object file or library. It 
can be distributed without the .a file or the .o files.

### Create a shared library

```gcc -shared bin/shared/add.o bin/shared/answer.o -o bin/shared/libtq84.so```

### Link dynamically

```gcc bin/main.o -Lbin/shared -ltq84 -o bin/use-shared-library```

Execute with:

```LD_LIBRARY_PATH=$(pwd)/bin/shared bin/use-shared-library```

### Export function

In .h file:

```extern "C" int Add(int a, int b);```

In .c file:

```
int Add(int a, int b)
{
    return a + b;
}
```

Show exports with:

```nm -D ./bin/shared/util.o```
