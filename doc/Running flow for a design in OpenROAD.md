# Running a basic flow for a design in OpenROAD

This document shows how to set up the platform and design configuration files for run the complete RTL2GDS flow using OpenROAD.

First, you need to source the ***setup_env.sh*** in the root directory of OpenROAD, (OpenROAD-flow-scripts)

```shell
source setup_env.sh
```

The necessary platform and design configuration files are in the directory called **flow**

```shell
cd flow
```

## Platform Configuration

For this configuration view the configuration file setup for **sky130hd**.

```shell
less ./platforms/sky130hd/config.mk
```

![](/home/atrujillo/Documentos/images/config_mk.png)

This config.mk file has all the required variables for sky130hd pdk platform and it is recommended that you don't change any variable definition.

If you want to know more about variables to customize your design flow go to this page [Flow Variables](https://openroad-flow-scripts.readthedocs.io/en/latest/user/FlowVariables.html).

## Design configuration

If you want to create a design you must have a design configuration file for run the flow, then, you need basic design-specific configuration variables, the variables are required to specify main design inputs such as the platform that have been used, the top-level design name and constraints.

| Variable Name        | Description                                                                                                                                    |
|:--------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------:|
| **PLATFORM**         | Specifies the PDK                                                                                                                              |
| **DESIGN_NAME**      | Specifies the name of the top-level module of the design                                                                                       |
| **VERILOG_FILES**    | The path to the verilog files of the design                                                                                                    |
| **SDC_FILE**         | Specifies the path of  the design constraints file                                                                                             |
| **CORE_UTILIZATION** | Specifies the core utilization percentage                                                                                                      |
| **PLACE_DENSITY**    | The desired placement density of cells, reflects how spread the cells would be on the core area. **1** = Closely dense. **0** = Widely spread. |

For understand the syntax of the design configuration file view the default design file for **ibex** design:

```shell
less ./designs/sky130hd/ibex/config.mk
```

![](/home/atrujillo/Documentos/images/design_config_1.png)

![](/home/atrujillo/Documentos/images/design_config_2.png)

This is a basic configuration for the **ibex** design.

## Running the automated RTL2GDS flow

The OpenROAD application executes an entire automatic flow usign TCL scripts that invoke open-sourced tools, from the synthesis to gds file creation without human intervention, however, in this section you will learn the automated and a few interactive ways to run the TCL commands for flow stages.

In the OpenROAD-flow-scripts directory, users can access to individual flow stages, respective tools and the corresponding readme for each tools and his commands, examples using TCL interface and another details.

OpenROAD uses some individual tools for the flow stages.

### Running the gcd design

For running the **gcd** design with the OpenROAD-flow-scripts automated flow you must follow a sequence of commands.

- Change your current directory OpenROAD-flow-scripts/ to flow/
  
  ```shell
  cd flow
  ```

- Run the complete flow with:
  
  ```shell
  make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk
  ```

**The complete flow for gcd takes 3 to 5 minutes it will vary based in your computer configuration**

If you have completed the RTL2GDS flow, then you proceed to view the final GDS file under results directory

`./results/sky130hd/gcd/base/`

If you have errors and you want to restart all flow, the files for sky130hd/gcd can be deleted with the next command

```shell
make clean_all DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk
```

If you want to delete files related to individual stages of the flow like synthesis, CTS routing or layout generation you can also delete with:

`clean_synth`, for delete the synthesis.

`clean_floorplan`, for delete floorplan.

`clean_place`, for delete the placement.

`clean_cts` , for delete the CTS.

`clean_route` , for delete the routing.

`clean_finish` , for delete the final stage.

### OpenROAD GUI

The GUI allows to select, highlight and navigate the design hierarchy and design objects (nets, pins, instances, paths, etc.) with the GUI you can see a detailed visualization and customization options.

In the OpenROAD GUI you can watch:

- Design hierarchy.

- Load ODB files for floorplan and layout visualization.

- Trace the synthesized clock tree to view hierarchy and buffers.

- Use heat maps to view congestion and observe the effect of placement.

- View and trace critical timing paths.

- Set display control options.

- Zoom to object from inspector.

For the `gcd` design uncomment the `DESIGN_CONFIG` variable in the `Makefile`

![](/home/atrujillo/Documentos/images/gui_1.png)

Use the next command

```shell
make gui_final
```

#### Viewing the layout results

The `make gui_final` command reads and loads the technology `odb` files and the parasitics, then, invokes the GUI in these steps:

The figure below shows the post-routed DEF for the gcd design.

![](/home/atrujillo/Documentos/images/gui_2.png)

At your LHS window shows the Display control that shows buttons for color, visibility and selection options for variuos design objects: *Layers*, *Nets*, *Instances*, *Blockages*, *Heatmaps*

In the RHS window shows a user interface to inspect details of a selected design objects and the timing report.

##### Showing the Clock Tree in the design

You can watch the synthesized clock for gcd design:

- From the top Toolbar click `view` - > `find`

- In the dialog box `find object` search the clock net using a keyword as follows:

![](/home/atrujillo/Documentos/images/gui_3.png)

Once you clicked **Ok** for can watch only the clock structure at the LHS window, disable *layers*

![](/home/atrujillo/Documentos/images/gui_4.png) 

##### Showing heat maps

From the Menu Bar, Click on Tools -> Heat Maps -> Placement Density to view congestion selectively on vertical and horizontal layers.

Expand Heat Maps -> Placement Density from the Display Control window available on LHS of OpenROAD GUI.

![](/home/atrujillo/Documentos/images/gui_5.png)

In the Placement density setup pop-up window, Select Minimum -> 50.00 Maximum -> 100.00%

![](/home/atrujillo/Documentos/images/gui_6.png)

From Display Control select Heat Maps -> Routing Congestion as follows:

![](/home/atrujillo/Documentos/images/gui_7.png)

From Display Control, select Heat Maps -> Power Density as follows:

![](/home/atrujillo/Documentos/images/gui_8.png)

![](/home/atrujillo/Documentos/images/gui_9.png)

---

### Tools used in OpenROAD

---

#### Synthesis (Yosys)

Is a framework for RTL synthesis tools. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains. [The-OpenROAD-Project/yosys](https://github.com/The-OpenROAD-Project/yosys/blob/master/README.md).

##### Database files (OpenDB)

OpenDB is a design database to support tools for physical chip design. The structure of OpenDB is based on the text file formats LEF (library) and DEF (design) formats version 5.6. OpenDB supports a binary file format to save and load the design much faster than using LEF and DEF. [OpenROAD/OpenDB](https://openroad.readthedocs.io/en/latest/main/src/odb/README.html)

##### Floorplan (Internal Algorithms)

OpenROAD uses an algorithm called **Initialize Floorplan**.

##### Pin Placement (Internal Algorithms)

OpenROAD uses an algorithm called **ioPlacer**.

##### Chip level connections (Internal Algorithms)

OpenROAD uses an algorithm called **ICeWall** and his function is place an IO ring around the boundary of the a chip and connect with either wirebond pads or a bump array.

##### Macro placement (Triton MacroPlacer)

ParquetFP-based macro cell placer, “TritonMacroPlacer”. The macro placer places macros/blocks honoring halos, channels and cell row “snapping”.

##### Tapcell insertions (Internal Algorithms)

##### PDN Analysis (Internal Algorithm)

OpenROAD uses an utility called **PDNGEN** and this utility aims to simplify the process of adding a power grid into a floorplan.

##### IR Drop Analysis (PDNSIM)

PDNSim is an open-source static IR analyzer [PDNSIM](https://openroad.readthedocs.io/en/latest/main/src/psm/README.html)

##### Global Placement (RePlAce)

RePlAce: Advancing Solution Quality and Routability Validation in Global Placement [RePIAce](https://openroad.readthedocs.io/en/latest/main/src/gpl/README.html)

##### Timing Analysis (OpenSTA)

Parallax Static Timing Analyzer or OpenSTA is a gate level static timing verifier. As a stand-alone executable it can be used to verify the timing of a design using standard file formats [Parallax/OpenSTA](https://openroad.readthedocs.io/en/latest/main/src/sta/README.html)

##### Detailed placement (OpenDP)

OpenDP: Open-Source Detailed Placement Engine [OpenDP](https://openroad.readthedocs.io/en/latest/main/src/dpl/README.html)

##### Clock Tree Synthesis (TritonCTS 2.0)

TritonCTS 2.0 is available under the OpenROAD app as **clock_tree_synthesis** command [TritonCTS OpenROAD](https://openroad.readthedocs.io/en/latest/main/src/cts/README.html)

##### Global Routing (FastRoute)

FastRoute is an open-source global router originally derived from Iowa State University’s FastRoute4.1 [FastRoute/OpenROAD](https://openroad.readthedocs.io/en/latest/main/src/grt/README.html)

##### Antenna Rule Check (Antenna Rule Checker)

This tool checks antenna violations and generates a report to indicate violated nets. [Antenna Rule Checker/OpenROAD](https://openroad.readthedocs.io/en/latest/main/src/ant/README.html)

##### Detailed Routing (TritonRoute)

TritonRoute is an open-source detailed router for modern industrial designs.[TritonRoute/OpenROAD](https://openroad.readthedocs.io/en/latest/main/src/drt/README.html)

##### Parasitics extraction (OpenRCX)

OpenRCX is a Parasitic Extraction (PEX, or RCX) tool that works on OpenDB design APIs. It extracts routed designs based on the LEF/DEF layout model [OpenRCX/OpenROAD](https://openroad.readthedocs.io/en/latest/main/src/rcx/README.html).

##### Layout Generation (Klayout)

[KLayout](https://www.klayout.de/)
