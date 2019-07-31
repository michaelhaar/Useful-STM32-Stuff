# Adding Header Files to a KEIL µVision Project

This document shows how to add header files (.h) to a KEIL µVision project.

# Motivation
I'm writing this tutorial, because I think the workflow is not very intuitive.

# Why we need Header Files
Adding source files (.c) to the project is pretty straight forward. Just right
click on a group in the project tree and click `Add New Item to Group` and
follow the instructions.

Adding header files although is not really intuitive, since µVision does not require you
to add header files in the project structure. You just need to tell the compiler
where the header files are located!

# Step by Step instructions
I usually start my projects with the STM32CubeMX software for the `MDK-ARM V5`
toolchain/IDE.
If you open the project directory in the explorer it will probably look like
this:
```
`PROJECT_NAME
|--- Core
|--- Drivers
|--- MDK-ARM
|--- .mxproject
`--- STM32_SPI_Test.ioc
```

I think it's very intuitive to collect my drivers, middleware and application
code in a folder named after the company I work for. This results in a folder structure like this:
```
`PROJECT_NAME
|--- Core
|--- Drivers
|--- DiniTech
|    |--- inc
|    |    `--- my_header_file.h
|    `--- src
|--- MDK-ARM
|--- .mxproject
`--- STM32_SPI_Test.ioc
```


## 1. Creating Header Files
1. Create a new file (STRG + N)
2. In the menubar select `File` and then `Save As`. Save it under
`DiniTech\inc\my_header_file.h` or wherever you want.

## 2. "Adding" Header Files.
We need to tell KEIL where the header files are located. Therefore we must follow the steps below:
1. In the menubar select `Project` and then `Options for Target`.
2. A new window will open. Click or switch to the `C/C++` tab.
3. There's a field called `Include Paths`. Hit the `...` icon right next to it.
4. Simply add the new path for the include files.

Have fun coding! =]
