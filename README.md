# OpenSSD-Rowhammer-Data
Repository of documentation and data files for the "Performing Rowhammer-like Attacks on OpenSSD NAND Flash" project completed at the Princeton-Intel REU '24.

For a more high level description, please view [the slides for this project](https://docs.google.com/presentation/d/1VuKDGHRU1CSy5wz2qb5-6ENMViEKH8YWHjf_TMDVi7Q/edit?usp=sharing).

- [Introduction](#introduction)
- [Project Setup](#project-setup)
    - [`nand_playground.c` Setup](#nand_playgroundc-setup)
- [Basic Operations](#basic-operations)
- [Playground Tasks](#playground-tasks)
- [Future Work](#future-work)
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

`r <die number> <block number> <page number> <column number>` - reads from the specified page in the specified block in the specified die, starting from the specified column number. Generally `<column number>` should be set to 0. 

`w <die number> <block number> <page number> <column number>` - write

`e` - erase

`d` - delay


## Playground Tasks
\*Note that only the Playground Tasks related to running the Rowhammer experiments will be described below.

### PLAY_WRITE_PATTERN

### PLAY_READ

### PLAY_REPEATED_READ
Repeatedly reads from a given die, block, page and columns a large number of times and checks for any errors that may be present. As of now read disturbance errors have not yet been found through repeated reads.

### PLAY_REPEATED_WRITE
Repeatedly reads from a given die, block, page and columns a large number of times and (optionally) checks for any errors that may be present. This Playground Task is sufficient to induce Rowhammer effects, however, it may be preferable to use [PLAY_HAMMER](#play_hammer) to view the number of resulting errors from writing various numbers of times.

### PLAY_ECC_SANITY

### PLAY_FIND_MAPPING

### PLAY_FIND_MAPPING_MULTIBLOCK

### PLAY_FIND_MAPPING_ECC

### PLAY_TEST_ECC_THRESHOLD

### PLAY_HAMMER

### PLAY_BYPASS_QUEUE_READ

### PLAY_MULTIPLANE

### PLAY_INTERACTIVE

### PLAY_TIME_READS

### PLAY_WRITE_PACKS

### PLAY_ERASE
Uses the `V2FEraseBlockAsync()` function to erase the given block at channel 0, then waits for the channel to be ready. 

### PLAY_COLWISE_PACKING

## Future Work
- Examine Flash Translation Layer (FTL) software
    - Further understand mapping
    - Examine request scheduling
- Research mitigations to full-system attacks
- Limit hammering effects to only victim and aggressor pages
- Lower minimum number of bits required for hammering effects

## References
Anil Kurmus, Nikolas Ioannou, Matthias Neugschwandtner, Nikolaos Papandreou, and Thomas Parnell. 2017. From random block corruption to privilege escalation: a filesystem attack vector for rowhammer-like attacks. In Proceedings of the 11th USENIX Conference on Offensive Technologies (WOOT'17). USENIX Association, USA, 4.

Laura M. Grupp, Adrian M. Caulfield, Joel Coburn, Steven Swanson, Eitan Yaakobi, Paul H. Siegel, and Jack K. Wolf. 2009. Characterizing flash memory: anomalies, observations, and applications. In Proceedings of the 42nd Annual IEEE/ACM International Symposium on Microarchitecture (MICRO 42). Association for Computing Machinery, New York, NY, USA, 24–33. https://doi.org/10.1145/1669112.1669118

Rino Micheloni, Alessia Marelli, and Kam Eshghi. 2012. Inside Solid State Drives (SSDs). Springer Publishing Company, Incorporated.

Yoongu Kim, Ross Daly, Jeremie Kim, Chris Fallin, Ji Hye Lee, Donghyuk Lee, Chris Wilkerson, Konrad Lai, and Onur Mutlu. 2014. Flipping bits in memory without accessing them: an experimental study of DRAM disturbance errors. In Proceeding of the 41st annual international symposium on Computer architecuture (ISCA '14). IEEE Press, 361–372.
