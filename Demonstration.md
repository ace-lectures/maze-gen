# Building a Maze Generator Walking Skeleton

## Step 1: Checking the dev environment

```
mosser@azrael maze-gen % mvn clean package
[INFO] Scanning for projects...
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.739 s
[INFO] Finished at: 2024-01-06T11:13:35-05:00
[INFO] ------------------------------------------------------------------------
mosser@azrael maze-gen % 

```

We're good to go, we can compile the project on any computer!

Let's now have a look at the business specification in the `README.md` file.


## Step 2: What Entry points do we need?

1. Create a `Main` class in the `ca.mcscert.se2aa4.tools.mazegen` package
    - This is configured as `mainClass` in the _POM_
2. Create the static entry point
    - Demonstrate the `psvm` shortcut in IntelliJ
3. Add a log statement
    - `private static final Logger logger = LogManager.getLogger();`
    - `logger.info("Here goes the business code")`
    - Demonstrate auto-import with Option-Space (and the fact that one should read before accepting suggestions)
4. Check that is works
    - `mvn package`
    - `java -jar target/mazegen.jar`
5. Add files, commit.

## Step 3: What are we doing?

We need to:
1. Read configuration from command line;
2. Build a reproducible random generator
3. Generate a Maze based on these two elements
4. Export it as a textual representation.

So let's write a `main` method that would reflect that logic in the code.

```
public static void main(String[] args) {
    logger.info("Here goes the business code");
    Configuration config = readConfiguration(args);
    Random random = buildReproducibleGenerator(config);
    Maze theMaze = new Maze(config, random);
    theMaze.export(config);
}
```

Wait! That does not even compile! This is not our problem right now, we're trying to understand what we're doing.

Even if it does not compile, the code looke dirty. Let's apply the "Boy Scout rule" and clean it up.

What problems are we encountering?

1. All the business logic is in `Main`
2. We're almost only relying on static methods, no object decomposition
3. The `Maze` concept does creation, carving and exportation at the same time
3. Everything is coupled to the `Configuration`

Let's fix these problems:

```
public static void main(String[] args) {
    logger.info("Here goes the business code");
    Configuration config = new Configuration(args);
    Random random = buildReproducibleGenerator(config.seed());
    Maze theMaze = new Maze(config.width(), config.height());
    theMaze.carve(random);
    MazeExporter exporter = new MazeExporter(theMaze);
    exporter.export(config.outputFile());
}
```

Design choice rationale:
- We need a `Configuration` concept that will contains the info provided by the user;
- We keep the `buildReproducibleGenerator(config.seed())` as a static method, it looks like a technical helper, not a real business-oriented feature;
- We separate the creation of the maze (`new Maze`) from its carving (`theMaze.carve()`);
- We separate the manipulation of the Maze from its exportation (`MazeExporter`).

## Step #4: Make it happen!

1. Demonstrate the create class feature (Option-Space in IntelliJ) to create the code skeletons for the missing classes and methods.
    - Remember to first create the arguments of the methods 
2. Compile the code with Maven
    - `mvn clean package`
    - What we've done is a refactoring: no new business value added, simply an evolution of the code structure.
3. We now have a very minimal _walking skeleton_!
    - add the files to version control, and commit. 

## Step #5: Mock complex features

We need to prioritize our work. What have more value?

1. Reading the Configuration?
2. Building the maze?
3. Exporting the maze?

So far, building and exporting seems to be the part with the highest value. Can we _mock_ the first part to allow us to focus on the two others?

The specification states:

- `-w` specifies the width of the maze, an odd integer greater or equals to 5. Default is 41.
- `-h` specifies the height of the maze, an odd integer greater or equals to 5. Default is 41.
- `-o` specifies the output file to use. Default is `stdout`
- `-s` specified the [seed](https://en.wikipedia.org/wiki/Random_seed) to be used by the random number generation (default is "now")
- `-human` indicates to the program to double the symbols used to print the maze, making it easier to read by a human.

So a _minimal_ configuration would be:
  - hard-coded value of `width` and `height`: `41`
  - using `stdout` as output
  - the `seed` is _now_ (number of milliseconds since EPOCH)
  - we don;t care about "human readable exportation" for now.

So a _mocked_ configuration could be:

```
public class Configuration {

    public Configuration(String[] args) { }

    public long seed() { return System.currentTimeMillis(); }

    public int width() { return 41; }

    public int height() { return 41; }

    public BufferedWriter outputFile() {
        return new BufferedWriter(new OutputStreamWriter(System.out));
    }
}
```

And yes, this is not the final version of the code. **It's technical debt**. But we know it, and we can focus on important things instead of wasting too much time now on how to parse command line.

1. Let's compile:
    - `mvn clean package`
2. Let's record what we have: Git add, git commit.

Before focusing on the maze generation process, we can fix the random generator once and for all.

```
private static Random buildReproducibleGenerator(long seed) {
    Random generator = new Random();
    generator.setSeed(seed);
    return generator;
}
```

1. Let's compile:
    - `mvn clean package`
2. Let's record what we have: Git add, git commit.

And... We're done! We now have a "walking skeleton" of our maze generator. It does not provide any business value to the end use, but it has a sound structure. And we can use this structure to focus on delivering value now. 

## Next Step (not covered in the demo)

What would be the minimal and viable product for the maze generator?