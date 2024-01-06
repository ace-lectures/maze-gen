# Maze Generator

![build status](https://github.com/ace-lectures/maze-gen/actions/workflows/build.yaml/badge.svg)

- Author: Dr. [Sébastien Mosser](https://mosser.github.io)
- Version: 2024.01
- Context: McMaster University > Engineering > Computing & Software > SFWRENG > 2AA4
- License: [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

## Description

This is a classical maze generation implementation, using a tile map approach to carve a maze through a grid of walls.
We use Prim's algorithm to support the generation. The algorithm is described in details in this [blog post by Jamis Buck](https://weblog.jamisbuck.org/2011/1/10/maze-generation-prim-s-algorithm). It basically consider a maze entirely made of walls, and carves a perfect maze out of it.

## How to use this software?

To compile the source code into a turn-key _Java ARchive_ (JAR):

```
mosser@azrael maze-gen % mvn -q clean package  
```

To run the generator:

```
mosser@azrael maze-gen % java -jar target/tennis.jar -w WIDTH -h HEIGTH  -o OUTPUT_FILE -s SEED -human 
```

- `-w` specifies the width of the maze, an odd integer greater or equals to 5. Default is 41.
- `-h` specifies the height of the maze, an odd integer greater or equals to 5. Default is 41.
- `-o` specifies the output file to use. Default is `stdout`
- `-s` specified the [seed](https://en.wikipedia.org/wiki/Random_seed) to be used by the random number generation
- `-human` indicates to the program to double the symbols used to print the maze, making it easier to read by a human.
