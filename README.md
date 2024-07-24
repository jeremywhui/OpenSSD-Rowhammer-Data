# OpenSSD-Rowhammer-Data
Repository of documentation and data files for the "Performing Rowhammer-like Attacks on OpenSSD NAND Flash" project completed at the Princeton-Intel REU '24.

For a more high level description, please view [the slides for this project](https://docs.google.com/presentation/d/1VuKDGHRU1CSy5wz2qb5-6ENMViEKH8YWHjf_TMDVi7Q/edit?usp=sharing).

- [Introduction](#introduction)
- [Project Setup](#project-setup)
    - [`nand_playground.c` Setup](#nand_playgroundc-setup)
- [Basic Operations](#basic-operations)
- [Playground Tasks](#playground-tasks)
- [Future Work](#future-work)
- [Video Demos](#video-demos)
- [References](#references)

## Introduction
This project aims to answer the following three questions:
1. Can Rowhammer be performed on 3D NAND Flash?
2. If so, how can Rowhammer be controlled and leveraged?
3. Can Rowhammer be used to bypass Error Correction Codes (ECC)?

The experimental setup of this project uses the Daisy+ OpenSSD Micron 4C2W LPDDR4 board, which serves to be a convenient experimental board for testing various experiments. This setup is interfaced using C microcode and Verilog HDL.

## Project Setup
The program initially runs `main.c`, which calls `nvme_main.c` to initialize the FTL as well as containing other tasks for NVMe. This file also calls `nand_playground.c` and is where almost all experiments for this project are run.

### `nand_playground.c` Setup
This file is begins with many basic flash operations in seperate methods, as well as many "playground tasks" to run various experiments to test the effects of Rowhammer on Flash. To test these operations separately, it is preferred to set the following line to have a `playground_task` of `PLAY_INTERACTIVE`:
```c
playground_task play_task = PLAY_INTERACTIVE;
```
These operations will be further described in [Basic Operations](#basic-operations). Further playground tasks are discussed in [Playground Tasks](#playground-tasks).

## Basic Operations

`q` - quits the program.

`r <die> <block> <page> <column>` - reads from the specified page in the specified block in the specified die, starting from the specified column. Generally `<column>` should be set to 0. 

`w <die> <block> <page>` - writes the first 64 columns to have the first bit flipped to 0 (2147483647 in decimal), and all other columns as all 1's (4294967295 in decimal) at the specified die, block, and page.

`e <die> <block>` - erases a specified block in a specified die.

`c <die> <block> <page> <column> <pattern>` -  counts the total number of errors and the number of pages with errors in a specified block, checked against a specified pattern.

`p <die> <block> <page> <column> <pattern>` - applies a given pattern.

`h <die> <block> <page>` - hammers the first 64 columns to have the first bit flipped to 0 (2147483647 in decimal), and all other columns as all 1's (4294967295 in decimal) by repeatedly writing this pattern 1048576 times. Executes this process at the specified die, block, and page.

`d` - performs a delay for 10 seconds. Used as a sanity check when performing Rowhammer in order to ensure bit flips do not occur from waiting.

`o <die> <block> <page> <column>` - reads from the specified page in the specified block in the specified die, starting from the specified column number like `r`, but instead only reads the next 24 words.

`1 <die> <block>` - erases the block, equivalent to the `e` command.

`2 <die> <block> <page> <column>` - reads with ECC decoding from a specified page.

`3` - writes with ECC encoding to a specified page.

`4 <die> <block> <page>` - writes all 1s with ECC encoding to all pages in a block. If the block is erased, the command simply encodes the contents.

`5 <die> <block> <page> <column>` - writes 1s up to a specified column in a page, then 0s for the rest of the page.

`6 <die> <block> <page>` - hammers all 0s to a specified page.

## Playground Tasks
\*Note that only the Playground Tasks related to running the Rowhammer experiments will be described below.

### PLAY_REPEATED_READ
Repeatedly reads from a given die, block, page and columns a large number of times and checks for any errors that may be present. As of now read disturbance errors have not yet been found through repeated reads.

### PLAY_REPEATED_WRITE
Repeatedly reads from a given die, block, page and columns a large number of times and (optionally) checks for any errors that may be present. This Playground Task is sufficient to induce Rowhammer effects, however, it may be preferable to use [PLAY_HAMMER](#play_hammer) to view the number of resulting errors from writing various numbers of times.

### PLAY_FIND_MAPPING
Iterates through each page and hammers, counts number of errors in each page and reports total number of pages with errors. Does not increment block.
### PLAY_FIND_MAPPING_MULTIBLOCK
Same as PLAY_FIND_MULTIBLOCK but increments the block and erases before hammering. Counts number of errors for each iteration both before and after hammering.
### PLAY_FIND_MAPPING_ECC
Same as PLAY_FIND_MAPPING_MULTIBLOCK but with ECC write and read functions. 
### PLAY_TEST_ECC_THRESHOLD
Write specified amount of 0s using ECC write to page. Then read and see if corrections were made.
### PLAY_HAMMER
Performs several iterations of hammering with exponentially increasing amounts of hammering, from 1 to 1048576. Count total number of errors in the block for each iteration. 
### PLAY_HAMMER_READS
Same as PLAY_HAMMER but with read operations.
### PLAY_INTERACTIVE
Allows for basic operations to be tested separately through a command-line interface.

### PLAY_ERASE
Uses the `V2FEraseBlockAsync()` function to erase the given block at channel 0, then waits for the channel to be ready. 

## Future Work
- Examine Flash Translation Layer (FTL) software
    - Further understand mapping
    - Examine request scheduling
- Research mitigations to full-system attacks
- Limit hammering effects to only victim and aggressor pages
- Lower minimum number of bits required for hammering effects
## Video Demos
- Cell targeted attack demos:
    - [Before hammering](https://drive.google.com/file/d/1jyn__EOjHA94RQtv2eHecDY3mRCNFb0E/view?usp=sharing)
    - [After hammering](https://drive.google.com/file/d/1SDJu3W-H8YpoyQU7D9pafaBuRR_XP6xB/view?usp=sharing)
- ECC attack demo:
    - [Before hammering](https://drive.google.com/file/d/1fMg7ExPKXOX3ifcKE7bsS4wqp9k2S2NB/view?usp=sharing)
    - [After hammering](https://drive.google.com/file/d/1_FkIkBRxCkiKDO84wvBTinXbO_T_v0xA/view?usp=sharing)
## References
Anil Kurmus, Nikolas Ioannou, Matthias Neugschwandtner, Nikolaos Papandreou, and Thomas Parnell. 2017. From random block corruption to privilege escalation: a filesystem attack vector for rowhammer-like attacks. In Proceedings of the 11th USENIX Conference on Offensive Technologies (WOOT'17). USENIX Association, USA, 4.

David Harris and Sarah Harris. 2012. Digital Design and Computer Architecture, Second Edition (2nd. ed.). Morgan Kaufmann Publishers Inc., San Francisco, CA, USA.

Laura M. Grupp, Adrian M. Caulfield, Joel Coburn, Steven Swanson, Eitan Yaakobi, Paul H. Siegel, and Jack K. Wolf. 2009. Characterizing flash memory: anomalies, observations, and applications. In Proceedings of the 42nd Annual IEEE/ACM International Symposium on Microarchitecture (MICRO 42). Association for Computing Machinery, New York, NY, USA, 24–33. https://doi.org/10.1145/1669112.1669118

Micron, NAND Flash Memory - Fortis Flash

Rino Micheloni, Alessia Marelli, and Kam Eshghi. 2012. Inside Solid State Drives (SSDs). Springer Publishing Company, Incorporated.

Yoongu Kim, Ross Daly, Jeremie Kim, Chris Fallin, Ji Hye Lee, Donghyuk Lee, Chris Wilkerson, Konrad Lai, and Onur Mutlu. 2014. Flipping bits in memory without accessing them: an experimental study of DRAM disturbance errors. In Proceeding of the 41st annual international symposium on Computer architecuture (ISCA '14). IEEE Press, 361–372.
