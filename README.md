Name: ................

#Assignment 4, Operating System
The current OS assignment covers large responsiblity, to fully reverse engineer IO Uring through the scheme explained on class previously. Based on this consideration the period to complete this assignment is extended into two weeks. On this directory, I have put io_uring from kernel source. However since only the sub directory was taken, on your IDE could shown many red error dependency warning. If you prefer, you may put the comment on your own kernel source, while later move the relevant directory to this repo.

## Task 1: Information about io_uring source
List in this section source and headers of io_uring. For each of the C source, you must put description what's the prime responsibily of the source. Take notes, description of the source should be slightly technical like the example given. 

### Source
#### advice.c
Store io_madvice & io_fadvice structures, both have the same exact attributes. Which make them basically the same thing. Except function body treat them as separate. Codes which make use of io_madvice are guarded by compilation macro, which make its relevant functions only active if the build flag is set. But functions that make use of io_fadvice are active all the time. The exact difference between io_madvice & io_fadvice will only known after exploring do_madvise function for io_madvice & vfs_fadvise function for io_fadvice. 
- .............

### Headers
- advice.h
Just declare the function specification

