## Compiler
- `gcc`: GNU C Compiler
  - ggc-2.0: released in 1992 and supported C++
  - gcc-4.0: released in 2005. This version was integrated into *Tree Serial Storage Architecture (SSA)*, and GCC evolved to be a modern compiler.
  - `gcc` will compile: `*.c\*.cpp` files as C and C++ respectively.
  - `gcc` compiling C files has fewer predefined macros.

- `g++`: GNU C++ Compiler
  - `g++` will compile: `*.c\*.cpp` files but they will all be treated as C++ files.
  - `g++` automatically links in the std C++ libraries
  - `g++` is equivalent to `gcc -xc++ -lstdc++ -shared-libgcc` (the 1st is a compiler option, the 2nd two are linker options).

- Low Level Virtual Machine (*LLVM*)
  - As the name suggests, Clang only supports C, C++, and Objective-C.
  - LLVM contains **a series of modularized compiler components and tool chains**.
  - After LLVM was developed, it became a collective term for many compilation tools and low-level tool technologies, making the name, "Low Level Virtual Machine", less appropriate. 
  - Now LLVM has become the official brand name, applicable to all projects under LLVM, including LLVM Intermediate Representation (LLVM IR), LLVM debugging tools, and LLVM C++ standard libraries. 
  - LVM can serve as a background for compilers in multiple languages.
  - In November 2011, LLVM 3.0 was released to become the default XCode compiler

- *Clang*
  - Clang is designed to provide a frontend compiler that can replace GCC.
  - *Clang* is a C, C++, Objective-C, or Objective-C++ compiler that is compiled in C++ based on LLVM.
  - Clang is mainly used to provide performance superior to that of GCC.

## Makefile
- Normally, you would compile `gcc -o hellomake hellomake.c hellofunc.c -I.`
  - This compiles the two `.c` files and names the executable hellomake.
  - `-o`: Write the build output to an output file.
  - `-Idir`: adds include directory of header files.
- Without a makefile, the typical approach to the test/modify/debug cycle is to use the up arrow in a terminal to go back to your last compile command.
```
some_file: other_file
	echo "This will run second, \
    because it depends on other_file"
  @echo "This make line will not be printed"
	touch some_file

other_file:
	echo "This will run first"
	touch other_file

.PHONY: clean
clean:
	rm -f some_file
	rm -f clean
```
- The target `some_file` “depends” on `other_file`. When we run make, `other_file` will get called first.
- The backslash (“\`) character gives us the ability to use multiple lines when the commands are too long
- Adding `.PHONY` to a target will prevent make from confusing the phony target with a file name
- `@` comment
```
hellomake: hellomake.c hellofunc.c
    gcc -o hellomake hellomake.c hellofunc.c -I.
```
- Type `make` on the command line it will execute the compile command as you have written it in the makefile.
- `make` **with no arguments executes the first rule (target)** in the file.
- Putting the list of files after the `:`, make knows that the rule **hellomake needs to be executed if any of those files change**.
- There must be a **tab** before the gcc command in the makefile.
```
CC=gcc
CFLAGS=-I.

hellomake: hellomake.o hellofunc.o
  $(CC) -o hellomake hellomake.o hellofunc.o
```
- The macro `CC` is the C compiler to use, and `CFLAGS` is the list of flags to pass to the compilation command. 
- There is one thing missing: dependency on the include files. 
- If you were to make a change to hellomake.h, for example, `make` would not recompile the `.c` files
- Reference variables using `${}` or `$()`
```
CC=gcc
CFLAGS=-I.
DEPS = hellomake.h
OBJ = hellomake.o hellofunc.o 

%.o: %.c $(DEPS)
	$(CC) -c -o $@ $< $(CFLAGS)

hellomake: $(OBJ)
	$(CC) -o $@ $^ $(CFLAGS)
``` 
- the macro `DEPS`, is the set of .h files on which the .c files depend
- The rule says that the `.o` file depends upon the `.c` version of the file and the `.h` files included in the DEPS macro.
- When there are multiple targets for a rule, the commands will be run for each target.
- `%`: matches any zero or more characters.
- `-c`: generate the object file
- `$@`: left side of the `:`.
- `$^`: right sides of the `:`.
- `$<`: the first item in the dependencies list.
- Implicit Rules 
  - `CC`: Program for compiling C programs; default cc
  - `CXX`: Program for compiling C++ programs; default G++
  - `CFLAGS`: Extra flags to give to the C compiler
  - `CXXFLAGS`: Extra flags to give to the C++ compiler
  - `CPPFLAGS`: Extra flags to give to the C preprosessor
  - `LDFLAGS`: Extra flags to give to compilers when they are supposed to invoke the linker
  - `n.o` is made automatically from `n.c` with a command of the form `$(CC) -c $(CPPFLAGS) $(CFLAGS)`
  - `n` is made automatically from `n.o` by running the command `$(CC) $(LDFLAGS) n.o $(LOADLIBES) $(LDLIBS)`
  
## CMake
```
cmake_minimum_required(VERSION 2.8.9)
project (hello)
add_executable(hello helloworld.cpp)
```
- For simple projects, a three line CMakeLists.txt file is all that is required. 
- The first line sets the minimum version of CMake for this project, which is major version 2, minor version 8, and patch version 9 in this example.
- `project()`: sets the project name.
- `add_executable()`: requests that an executable is to be built using the helloworld.cpp source file
- Execute the `cmake` command and pass it the directory that contains the source code and the CMakeLists.txt file.
- CMake identified the environment settings for the device and **created the Makefile for this project**
- Do not make edits to this Makefile, as any edits will be overwritten the next time that the cmake utility is executed.
- CMake puts generated under your current directory instead of the same directory as `CMakeLists.txt`.
- Run `cmake -S . -B build` to put your output under `build` directory.
```
cmake_minimum_required(VERSION 2.8.9)
project(directory_test)

# Bring the headers
include_directories(include)

# Can manually add the sources using the set command as follows:
# set(SOURCES src/mainapp.cpp src/Student.cpp)

# However, the file(GLOB...) allows for wildcard additions:
file(GLOB SOURCES "src/*.cpp")

add_executable(testStudent ${SOURCES})
```
- In this example that any header files (.h) are placed in the `include` directory and that the source files (.cpp) are placed in the `src` directory.
- As your project grows, it is likely that you will organize it into sub-directories. 
- `include_directories()`: bring the header files into the build environment.
- `set(SOURCES … )`: set a variable (`SOURCES`) that contains the name values of all of the source files (.cpp) in the project.
- `file()`: add the source files to the project. `GLOB` is used to create a list of all of the files that meet the globbing expression.
- If you add new source files to your project it is very important that you call the cmake program again.
```
cmake_minimum_required(VERSION 2.8.9)
project(directory_test)
set(CMAKE_BUILD_TYPE Release)

include_directories(include)
file(GLOB SOURCES "src/*.cpp")

# Generate the shared library from the sources
add_library(testStudent SHARED ${SOURCES})

# Set the location for library installation -- i.e., /usr/lib in this case
# not really necessary in this example. Use "sudo make install" to apply
install(TARGETS testStudent DESTINATION /usr/lib)
```
- `set(CMAKE_BUILD_TYPE Release)`: sets the build type to be a release build.
- `add_library()`:  The library is **built as a shared library** using the SHARED flag.
- `install()`: defines an installation location for the library. Deployment is invoked using a call to `sudo make install`.
- Run ` cmake --install build` to directly install library (CMake 3.15+ only).
 
```
cmake_minimum_required(VERSION 2.8.9)
project(directory_test)
set(CMAKE_BUILD_TYPE Release)
 
include_directories(include)
file(GLOB SOURCES "src/*.cpp")
 
#Generate the static library from the sources
add_library(testStudent STATIC ${SOURCES})
 
install(TARGETS testStudent DESTINATION /usr/lib)
```
- A statically-linked library (`.a`) is created at compile time to contain all of the code code relating the library. It makes copies of any dependency code, including that in other libraries.
- Static librart is typically larger in size than the equivalent shared library, but because **all of the dependencies are determined at compile time, there are fewer run-time loading costs and the library may be more platform independent**.
- Unless you are certain that you require a static library (`.so`), you should use a shared library as there will be fewer code duplications and the **shared library can be updated without recompilation.**
- You can determine the constituents of a static library using the GNU `ar -t` (archive) command.
- You can also use the GNU `nm -C` command to list the symbols in object files and binaries. (T is code, U is undefined, R is read-only data)
```
cmake_minimum_required(VERSION 2.8.9)
project (TestLibrary)
 
# For the shared library:
set ( PROJECT_LINK_LIBS libtestStudent.so )
link_directories( ~/exploringBB/extras/cmake/studentlib_shared/build )
 
#For the static library:
#set ( PROJECT_LINK_LIBS libtestStudent.a )
#link_directories( ~/exploringBB/extras/cmake/studentlib_static/build )
 
include_directories(~/exploringBB/extras/cmake/studentlib_shared/include)
 
add_executable(libtest libtest.cpp)
target_link_libraries(libtest ${PROJECT_LINK_LIBS} )
``` 
- `target_link_libraries()`: link the static or shared library to your project.

## Bazel

