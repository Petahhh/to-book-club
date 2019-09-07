# Chapter 12 - Components

**definition**: Smallest unit of a system that can be deployed independently

e.g. ruby gems, java jar files, 


## History of Components

* early programs needed to **specify where the program is loaded in memory** of the computer
* programs were compiled with their dependencies and as a result the compilation took a really long time and was **too slow**
* to solve for this dependencies were compiled and stored in specific memory addresses
* as programs grew - they grew out of the space allotted for them and so they had to be split up into different spots in memory which was not sustainable

### Relocatability

* to address the problem of fragmented code in memory we can relocate the code (move it around in memory instead hard coding it into a specific place in memory)
  * to do this the compiler had to create binaries that were "movable"
    * in practice, this was possible because of flags with (in?) the binary that specified which parts of the code needed to be modified for the binary to be moved
  * and the loader became smarter so that they can move the binaries (and in the process, modify them in the appropriate spots via the flags)
* this allowed programmers to load only the functions they needed
* the compiler was updated to emit metadata:
  * _external reference_ and
  * _external definitions_
  * with these two the loader could link references and definition (which was the birth of the linking loader)

### Linkers

* linkers allowed programmers to write many small programs that the linking loader could load
* as things evolved and the scale of small programs grew the linker **again became too slow:**
  * to address this the linking was split into two parts:
    * _a linker application_: written by programmers to link their code which was super slow but resulted in a relocatable program
    * the loader was able to load the linker application very quickly
* in the 80's programmers started using C where `.c` files were compiled into `.o` files - each individual module can be compiled very fast
* but then as that scaled up, compiling everything together **again became too slow**

#### Murphy's Law of program size

> Programs will grow to fill all available compile and link time.

* the theme of 60-80's was things got faster but then as we scaled up it wasn't enough
* then came:

#### Moore's law

> Computer speed, memory and density double every 18 months

* from the mid 90's on the hardware far outpaced the scale of the software
* linking modules times are insignificant and now we're in a world where dynamically linked (at runtime) modules are effortless and part of every day life
