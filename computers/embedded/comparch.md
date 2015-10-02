# Acronyms
* IPC = Instructions Per Cycle
* Processes:
    1. HKMG = High-k Metal Gate process
    2. HPM = High Performance Mobile process
    3. LP = Low Pwer process
* PMIC = Power Management IC
* ASV = Adaptive Scaling Voltage
* SKU = Stock Keeping Unit (an item)
* DVFS = Dynamic Voltage and Power Scaling (software-controlled)
* SOI = Silicon on Insulator
* FD-SOI = Fully Depleted Silicon on Insulator

### Trivia ###
* The traditional chip to test a new tech on is SRAM. Because of its 6T array
(regularity) it provides good data on how many defects a manufacturing process
produces.
    - Also, typically uprocessor makers have designs for SRAMs that are a year
    or more ahead of their processor designs.



ARM big.LITTLE
==============

## Cache Coherency Interconnect (CCI-400)
    - Allows heterogeneous multiprocessing between different CPU architectures.


- Panel self-refresh (displays)
- Median chip bin
- Block and die size


Product binning
================
* Due to process variations, not all chips come out performing to their
desired electrical characteristics which handicaps them from performing
at its target specifications.
* To avoid a complete waste of such chips, silicon vendors disable parts of
the chip to recycle a 'bad piece' as a lower-priced item.

* In mobile CPUs, instead of disabling the silicon blocks, manufacturers
increase voltages to compensate for process variations and then group the
parts with similar electrical characteristics into 'bins'.

* For example, in the Exynos chipsets these bins are called ASV groups i.e.
the chips are grouped according to their Adaptive Scaling Voltages.
    1. Higher voltage groups (bins) represent 'slow' or 'cold' silicon.
    * The manufactured transistors were not able to reach the design frequency
    without increasing the voltage levels (i.e. the threshold voltage is high).
    * Advantage: Lower static leakage (higher threshold voltages, low leakage)
    2. Lower voltage bins represent 'fast' or 'hot' silicon.
    * Lower threshold voltages; able to hit the desired clock rate at lower
    VDD.
    * More static leakage due to lower Vt.
    * But since, for most chips today:
        * Static leakage can mostly be mitigated by "Power Gating" mechanisms.
        * FinFET also dramatically reduces static leakage wrt planar transistors.
        * Because of these, power consumption today is mostly determined by *dynamic leakage*,
          that is:
            1. Gate to Drain and
            2. Drain-Induced-Barrier-Lowering (DIBL).
    * Thus, it is always better to have more chips in the hot/fast bin at lower
    operating voltages.

Power Management
================
* See the points on static vs dynamic leakage power above.

## DVS (Samsung technology) = Dynamic Voltage Scaling
* Similar to TI's PowerWise technology.
* Could replace the power/voltage based characterization (binning process).
* Qualcomm (Snapdragon) and Nvidia (Tegra K1) use closed-loop voltage
control mechanisms (like DVS) and have dropped 'fixed' binned voltage
tables.
* Closed loop system run by a power controller (Cortex M3) which
monitors the Hardware Performance Counters (HPM) to choose a particular
voltage to run the chip on. It tells the chip's volt regulator to
apply this voltage.
* The Cortex-M3 based power manager is called APM (Adaptive Power Manager).
* It communicates to the main system via Mailbox messages.
    - Mailboxes: a method of inter-processor communication.
        * Each processor can *write* messages only to their own designated mailbox
        (a chunk of RAM) and *read* messages from _all_ other mailboxes.
* The software controlled DVFS mechanism can send target voltages to the
APM whenever there is a frequency change.
* Initially, the software (kernel and the main CPU) programs the PMIC
regulator directly via I2C interface.
* Later, the control is handed over to the APM until next frequency change.
* The APM, then, measures HPMs (one each on the CPU clusters, GPU and memory controllers)
* When the APM sees the voltage threshold fall (due to temperature or other
influences changing the silicon characteristics), it lowers the voltages
below the rated voltage of the device (determined by the binning process).
    ** TLDR: It utilizes the variation of the silicon's threshold voltage
    to lower the power consumption below the stock (rated) value.
*Advantages*
* More fine-grained voltage control.
* Realtime response (changes voltages at 1ms interval) compared to the
20-80ms interval used by the DVFS mechanism.

* Voltage steps i.e. the resolution of the voltage control depends on the
PMIC used. Ex: Galaxy S6 has 6250 uV granularity on main buck converters.
* There is a limit on how far the APM is allowed to undervolt i.e. how far
can the APM be allowed to take the voltage below the rated value.
    - For ex. currently the value is 25 mV for CPU cores and 12.5 mV for
    GPU and memory controllers on Samsung SoCs.


# CMOS
### High-k Metal Gate processes:
    - High-k dielectric = material with high dielectric const (k) wrt SiO2.
    - Used to replace a SiO2 based gate dielectric.
    - Used to miniaturize uelectronic components (presumably SiO2 doesn't allow
    this beyond a limit?)
    - Due to rapid scaling of transistors, the SiO2 dielectric thickness has
    steadily decreased to increase gate capacitance => drive more current and
    raise performance.
        - Drain current = W/L*mobility*Gate Capacitance*Voltage
    - But, thickness < 2 nm => leakage currents due to *tunneling* increase.
    - So, replace SiO2 with a high-k material to allow increased gate
    capacitance (presumably C is directly proportional to k) without
    increasing the leakage current.
        C = k*(permittivity)*(capacitor area)/(thickness of oxide).
    - Earlier, a small amount of nitrogen was infused into the SiO2 to
    increase the dielectric constant (more inert element).
    - Nowadays, Hafnium-based high-k dielectrics are used likely,
    Nitrided Hafnium Silicates (HfSiON).
    - To allow gate to be adjusted to low threshold voltages, *metal* gates
    are used instead of *polysilicon*.
    - HKMG = Metal Gate + High-k dielectric = Reduces leakage => increases
    capacitance.
       - It's difficult to correctly make the dielectric layer. The high-k
    dielectric layers cannot be properly constructed with the traditional
    techniques of
        (a) Reactive Sputtering
        (b) Metal Organic Chemical Vapor Deposition
    These techniques produce reasonably smooth surfaces but aren't smooth
    enough which causes charges to get 'trapped' at the interface between the
    gate electrode and the dielectric.
        * Hence, _Atomic Layer Deposition_ is now used to lay the dielectric
        layer.
#### Atomic Layer Deposition ####
    - Lets you build up a material one layer of atoms at a time.
    - Introduce a gas which reacts with the surface of the Si wafer.
    - This leaves the substrate coated with a single layer of atoms.
    - When there is no more surface left to react with, the deposition stops.
    - The gas is then evacuated from the chamber and a second gas introduced.
    - The second gas is reactive to the layer of atoms just deposited.
    - It lays down another layer and stops when no atoms are left to react.
    - You can repeat the process multiple times.
    - Produces layered materials with thickness is controllable to a _single atom_.
    - HKMG processes: "gate-first" and "gate-last" approach
#### Reasons for using Metal gate ####
    - TLDR: The particular density of electrons in polysilicon (partially ordered Si;
    earlier material used for gate) allowed _inherent_ vibrations in the high-k
    dielectric to move into the transistor channel and disrupt the flow of current.
    Metals have a higher density of electrons which 'screened out' the vibrations
    and allowed the current to flow more smoothly. (WUT?)
    - The transistors made with the high-k insulators faced two problems:
        1. It took more voltage to turn them on than it should have = Fermi
           Level Pinning.
        2. Once they were on. the charges moved sluggishly through them slowing
           down the device's switching speed = Low Charge-Carrier Mobility.
    - Troubleshooting these problems, people found out that the problem lay
    in the interaction between the polysilicon gate and the high-k dielectric.
    - The dielectric is made of dipoles (objects with +ve and -ve poles) which
    give the dielectric their high dielectric constant.
    - These dipoles tend to vibrate like a taut rubber band and lead to
    strong vibrations in the semiconductor crystal lattice called *Phonons*.
    - These _Phonons_ knock around passing electrons, slowing them down and
    reducing the speed at which the transistor can switch.
    - Apparently, the influence of dipole vibrations on the channel vibrations
    can be screened out by increasing the density of electrons on the gate
    electrode.
    - The metal gate screens out the phonons (HOW?) and lets the electrons flow
    through the channel smoothly.
    - The bond betweent the high-k dielectric and the metal gate is 'much better'
    (what does that mean?) that the Fermi Level Pinning problem is solved as
    well.
    - The metal needs to have a similar _Work Function_ as Polysilicon.
    - Workfunction = Energy of electron in gate electrode relative to energy in
    electron in lightly doped silicon channel.
    - The energy difference (workfunction) sets up electric field that
    modulate the amount of voltage needed to turn the transistor on
    (threshold voltage). Gate's workfunction decides threshold voltage.
    - *Gate-first*:-
        * Usual industry-standard; however difficult to work with in HKMG.
        * Gate dielectric and gate electrodes are constructed first.
        * Then the dopants for the source & drain are implanted into the
        silicon on either side of the gate.
        * Then the Silicon is annealed to repair the damage from the
        implantation process.
        * That procedure requires that the gate electrode material be able to
        withstand the high temperatures used in the annealing step - BIG
        problem for _metals_ but not so much for polysilicon.
    - *Gate-last*:
        * Current HKMG industry-standard.
        * Gate electrode materials deposited _after_ the source & drain are
        formed.
        * Easier because thermal annealing problem is circumvented.
    - *Fully Silicided Gates*:
        * Use the gate-first approach to construct the polysilicon gate.
        * Then convert it into a metal-silicide gate (metal = Nickel, usually).
        This is done by essentially replacing alternate Si atoms with Metal.
        * Then dope the Ni-Silicide to alter it's workfunction for use in
        NMOS or PMOS devices.
        * However, in practice, altering the workfunction to the desired value
        isn't easy => not so widely used.


## FinFET/Trigate (3D) transistors
    - Improves the Vt mismatch due to process variations i.e. transistors tend
    to have more uniform threshold voltages.

- BEOL (Back-End-Of-Line): chip's largest metal layer?
- CPP : contacted gate-poly pitch?
- M1 metal layer?
- Die size measurement
- Patterning

### SRAM Scaling
* Core size shrinks with process node (28->20 nm) but the overall cluster
size reduces by much less due to the cache (SRAM) having a much lower
shrinking factor i.e. difficult to shrink cache than logic blocks.

* This is due to minimizing transistor variation which can make SRAM cells
unstable.
* Maybe poor noise margins too.

* SRAM scaling challenge twofold: cell stability & physical area.
* V_min for SRAM = function of mismatch in threshold voltage.
    - Vt mismatch inversely proportional to size of device/cell.
    - Larger cell => better stability.
* Threshold voltage mismatch has two components:
    - Structural
        * Variability higher in a 3D device compared to planar device.
    - Random Dopant Fluctuations (RDF)
        * Fully depleted devices (FD-SOI or finFET) minimize RDF mismatches.



One must keep in mind that auxiliary logic such as PLLs, bus interfaces,
and various other small blocks are part of a CPU cluster and may also
impact the effective scalability.

* Artificially scaling CPU core sizes to control power consumption.

Interconnects
=============
* Earlier Aluminum interconnects were used - now Copper ones.
    - Also changed from SiO2 'cladding' to chemically similar low-k dielectrics.
        * Dielectric constant = a material's ability to 'concentrate' electric
        field.
        * Higher k => insulator can provide more capacitance between two
        conducting plates.
        * When places in an electric field, the charges in a dielectric's
        atoms/molecules reorient themselves in the direction of the field.
        * These internal charges are more responsive in high-k dielectrics than
        in low-k dielectrics. Hence, the better 'block' flowing charges,
        thereby increasing the capacity of the capacitor.
                        *FRAGMENTATION, CONSIDER REVISING*
        * Interconnects use low-k dielectrics (lower-k than SiO2, that is).
        This is because, in the case of interconnects we don't want the
        electric field from a wire to be felt in the nearby wires. That creates
        a capacitor between the wires and interferes with or slows down the
        signals on them. A low-k dielectric fixes this problem.



# Branch Predictors
* Conditional and Indirect predictors
- gshare predictor and path history

# ARM Cortex A lineup
* A7 -> A53 (AArch64)
* A15 -> A57 -> A72

# Synthetic performance benchmarks
* SPECint 2000 (for mobile CPUs; retired for PC processors)
* Geekbench 3

# Caches
* Configurable cache size?
* Interleaved parity/SECDED ECC caches?
* More associative caches (meaning?)
* Decoded uop cache vs Execution Trace Cache difference


# Process Node vs Architectural Choices
* Architecture: Power vs Performance tradeoffs
* Process Nodes: If at a smaller node, power efficiency *increases* but
  performance gains now influenced by manufacturing factors:
  * Leakage
  * Viability of higher clocks
* Higher IPC design => *Clock* can be ramped down thus reducing the volt and
power consumption. Because, the design is not *as* reliant on the clock to
be performant.

# Register Renaming
* Maps lower count of architecturally visible registers (ISA registers) to
a larger pool of dynamically controlled registers
