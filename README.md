# asap7_contest

![7nm](./images/7nm.png)

# PPA Improvement of ibex using OpenROAD Flow Scripts

# Tool Flow of OpenROAD Flow Scripts:


![flow](./images/flow.png)

#
# Design Name: Ibex


![Literature survey report](./asap7_contest.pdf)

![Final report](./final_report.pdf)



# Steps followed:


1.Build and install using `./build_openroad.sh`

2.Comment the ibex/config.mk in the makefile at OpenROAD-flow-scripts/flow folder

![make](./images/make.png)

3.Run `make` 

4.Note the timings from the logs

5.Repeat from Step 1 again

#
# Challenges :

1. Finding proper pin placement.

2. Maintain proper cluster size and diameter to gain best clock skew.

3. Finding the appropriate buffer or clock inverter cell for the better timing.

4. High routing congestion while increasing the clock ferquency.

5. Distributing the metal later property for clock tree, signal net and pin to get better PPA without zero -ve skew.

6. Keep the design DRC free while increasing the clock frequency.


#
# Observation :

1. In the library file there wasn't any clock buffer cell so we had to use the normal buffer cell and clock inverter.

2. While increasing the clock frequency we observe some DRC violations at routing stage.


#
# Changes done in design :

`Floorplan:`

Set unconstraints paths into false path.

`CTS:`

Changed the CTS cell.
Defining clock routing layer.


#
# Make changes :

In `2_floorplan.sdc` file we updated the false path to clear the unconstrained paths and endpoints which are `TIE` low cells. In `constraint.sdc` file changed the clk io percentage into 0.15 that is  `set clk_io_pct 0.15` to improve the timing of the design.

```
#set_input_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {rst_ni}]
set_false_path -from [get_ports {rst_ni}]

#set_output_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {alert_major_o}]
set_false_path -to [get_ports {alert_major_o}]
#set_output_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {alert_minor_o}]
set_false_path -to [get_ports {alert_minor_o}]

#set_output_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {data_addr_o[0]}]
set_false_path -to [get_ports {data_addr_o[0]}]
#set_output_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {data_addr_o[1]}]
set_false_path -to [get_ports {data_addr_o[1]}]
#set_output_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {instr_addr_o[0]}]
set_false_path -to [get_ports {instr_addr_o[0]}]
#set_output_delay 352.0000 -clock [get_clocks {core_clock}] -add_delay [get_ports {instr_addr_o[1]}]
set_false_path -to [get_ports {instr_addr_o[1]}]

set_false_path -from [get_pins _36182_/CLK]
set_false_path -from [get_pins _36313_/CLK]
```

In `cts` stage we changed CTS cell list in `platforms` `config.mk` file for our design to improve the timing.

```
export CTS_BUF_CELL  ?= BUFx8_ASAP7_75t_R
```



#
# Final report:

# Before changes:

![before](./images/before.png)

![before1](./images/before_power.png)


# After changes:

![after](./images/after_final.png)

![after1](./images/after_power.png)
