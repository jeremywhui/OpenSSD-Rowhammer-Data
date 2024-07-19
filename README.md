# OpenSSD-Rowhammer-Data
Repository of documentation and data files for the Princeton-Intel REU '24.

- [Introduction](#introduction)
- [Project Setup](#project-setup)
    - [`nand_playground.c` Setup](#nand_playgroundc-setup)
- [Basic Operations](#basic-operations)
- [Future Work](#future-work)
- [References](#references)

## Introduction
TK

## Project Setup
The program initially runs `main.c`, which calls `nvme_main.c` to initialize the FTL as well as containing other tasks for NVMe. This file also calls `nand_playground.c` and is where almost all experiments for this project are run.

### `nand_playground.c` Setup
This file is begins with many basic flash operations in seperate methods, as well as many "playground tasks" to run various experiments to test the effects of Rowhammer on Flash. To test these operations separately, it is preferred to set the following line to have a `playground_task` of `PLAY_INTERACTIVE`:

```c
playground_task play_task = PLAY_INTERACTIVE;
```
These operations will be further described in [Basic Operations](#basic-operations).

## Basic Operations
TK

## Future Work
TK

## References
Anil Kurmus, Nikolas Ioannou, Matthias Neugschwandtner, Nikolaos Papandreou, and Thomas Parnell. 2017. From random block corruption to privilege escalation: a filesystem attack vector for rowhammer-like attacks. In Proceedings of the 11th USENIX Conference on Offensive Technologies (WOOT'17). USENIX Association, USA, 4.

Laura M. Grupp, Adrian M. Caulfield, Joel Coburn, Steven Swanson, Eitan Yaakobi, Paul H. Siegel, and Jack K. Wolf. 2009. Characterizing flash memory: anomalies, observations, and applications. In Proceedings of the 42nd Annual IEEE/ACM International Symposium on Microarchitecture (MICRO 42). Association for Computing Machinery, New York, NY, USA, 24–33. https://doi.org/10.1145/1669112.1669118

Rino Micheloni, Alessia Marelli, and Kam Eshghi. 2012. Inside Solid State Drives (SSDs). Springer Publishing Company, Incorporated.

Yoongu Kim, Ross Daly, Jeremie Kim, Chris Fallin, Ji Hye Lee, Donghyuk Lee, Chris Wilkerson, Konrad Lai, and Onur Mutlu. 2014. Flipping bits in memory without accessing them: an experimental study of DRAM disturbance errors. In Proceeding of the 41st annual international symposium on Computer architecuture (ISCA '14). IEEE Press, 361–372.
