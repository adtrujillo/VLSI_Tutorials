# Introducing the OpenROAD environment, tools and flow

In this tutorial, we introduce the structure of the OpenROAD environment, database access, features and tools.

For understanding the structure and how OpenROAD performs and autonomous flow we must know the basic structure for perform a complete design flow:

![Diagram flow](./images_introduction/diagram.png)

## Understanding the autonomous flow

The process begins with performing some preprocessing actions to set up the parameters, organize the libraries and tech files, and warp macros. Next, the logic synthesis stage is executed, whereby an RTL design is transformed into a mapped netlist.

Subsequently, the physical design process, consisting of Floorplanning, Placement, Clock Tree Synthesis, and Routing, is carried out. Finally, a finishing step is performed to generate the GDS file and relevant reports.

All tools in the physical design flow use a data model called OpenDB. This tool creates a database that can be directly accessed from the `openroad` environment using TCL commands.

All steps in the physical design flow are performed within the `openroad` environment, except for detailed routing.

## How to run the flow

OpenROAD autonomous flow is based on a makefile, provides a excellent functionality for caching and resuming failed stages from the previous successful stage.

The flow configuration is defined in `flow/Makefile` and in the beginning of the file you can watch some available configurations for different technologies, uncomment someone that you are interested to run.

By default in the makefile is available a design configuration called gcd, with the nangate45 technology

```shell
.
.
.
# Default design
DESIGN_CONFIG ?= ./designs/nangate45/gcd/config.mk
.
.
.
```

If you want run other design comment the default design and uncomment the design you want run. in the terminal, run `make` and this command start the automatic flow and run all design stages.

## Excersices

- From the `makefile`, what is the top level module if you uncomment the first design called aes with nangate45?

- From the `makefile`, can you locate where the script used for synthesis, floorplan, placement, CTS, Routing, and the finishing is?

- Where are the source files for the designs located?

- If you run a flow, what is the final design area for the gcd flow?

- What is the current clock period for the gcd design? Can you change it?
