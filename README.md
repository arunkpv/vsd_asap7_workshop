# FinFET Circuit Design and Characterization
This GitHub repository documents the 10-day workshop on [FinFET Circuit Design and Characterization using ASAP7 PDK](https://www.vlsisystemdesign.com/7nm) offered by VSD Corp. Pvt. Ltd. attended from 27 Aug - 05 Sept, 2025.
<br/>

**Table of Contents**

 | Module # | Topic(s) Covered | Status |
 |---|---|---|
 |[**Mod. 1**]() | **Scaling Beyond CMOS: FinFET Devices and Innovations** <br> <ol> <li>[Path To Zetta Scale Computing]()</li> <li>[Introduction To FinFETs]()</li> <li>[FEOL Innovations]()</li> <li>[BEOL Innovations]()</li> </ol> | ![](https://progress-bar.xyz/50/?title=InProgress) <br> **Documentation Pending** |
 |[**Mod. 2**]() | **Lab-to-Simulation: 7nm FinFET Inverter Performance Analysis** <br> <ol> <li>[First NFET Characteristics Using 7nm PDKs]()</li> <li>[First Inverter Characteristics Using 7nm FinFETs]()</li><li>[Module 2 Assignment - 7nm Inverter Characterization]()</li> </ol> | ![](https://progress-bar.xyz/50/?title=InProgress) <br> **Documentation Pending** |
 |[**Mod. 3**]() | **Design of a BandGap Reference Circuit** <br> <ol> <li>[Theory: Design of a BGR Circuit]()</li> <li>[Module 3 Assignent - Bandgap Reference Design and Simulation using Xschem]()</li> </ol> | ![](https://progress-bar.xyz/50/?title=InProgress) <br> **Documentation Pending** |

**NOTE: Theory part of the workshop needs to be updated in the documentation**
## 1 - Scaling Beyond CMOS: FinFET Devices and Innovations
### 1.1 - Path To Zetta Scale Computing
#### 1.1.1 - CMOS Evolution And Next-Gen Candidates
### 1.2 - Introduction To FinFETs
### 1.3 FEOL Innovations
#### 1.3.1 - CMOS Technology Inflection Points
#### 1.3.2 - Standard Cell Area Scaling And Variability
#### 1.3.3 - Parasitics Resistance And Capacitance
#### 1.3.4 - Device Scaling And Electrical Characteristics
#### 1.3.5 - 3D-Structures
### 1.4 - BEOL Innovations
_________________________________________________________________________________________________________  

## 2 - Lab-to-Simulation: 7nm FinFET Inverter Performance Analysis
The way the MOS model and OSDI files were all packaged into the symbol files
and the subckt wrapper definition resulted in the transistor parameters l=7e-9 and nfin=14 getting hardcoded for both pmos and nmos, even if each instance's values are modified in xschem. This required us to edit the generated netlist manually every time a schematic change was made in xschem.  

Moreover, if there is only 1 instance each of nmos & pmos, since it doesn't cause any issues,
we may overlook the effect of the hardcoding if there are more instances reulting in wrong sizes for the transistors.  

The recommended way to include OSDI lib is provided in the ngspice manual:
https://ngspice.sourceforge.io/docs/ngspice-html-manual/manual.xhtml#magicparlabel-21886  

The asap7 directory in the repo contains the modified files to follow the above method to use the OSDI models.  

  - The **asap7** folder needs to be placed in the directory where we are creating the schematics in xschem.
  - When using this with xschem, make the below changes in xschemrc
  From terminal:
  ```
  gedit ~/.xschem/xschemrc &
  
  # Change line no. 111 to:
  set netlist_dir $env(PWD)
  
  # Change line no. 118 to:
  set local_netlist_dir 0
  ```
  - Add the `asap7.spice` file using `.include` SPICE directive
  - Pre-load the OSDI models in netlist using `pre_osdi` command inside `.control` section

### 2.1 - NFET DC Characteristics Using 7nm PDKs
#### 2.1.1 - Parameter Shmoo with `alter`
<details> <summary> SPICE File: nfet_char.spice </summary>

```
** sch_path: /home/vsduser/Desktop/asap_7nm_Xschem/nfet_char.sch
**.subckt nfet_char
VGS G GND 0.8
VDS D GND 0.8
Xnfet1 D G GND GND asap_7nm_nfet l=7e-9 nfin=14
**** begin user architecture code
* ==========================================
* NFET DC Characterization
* ==========================================
.include ./asap7/asap7.spice

.control
pre_osdi ./asap7/bsimcmg.osdi
run

let VDS_Val     = 0
let VDS_Max     = 0.8
let VDS_incr_step = 0.1

let VGS_Val     = 0
let VGS_Max     = 0.8
let VGS_incr_step = 0.1

** TRANSFER CHARACTERISTICS
dowhile VDS_Val <= VDS_Max
    dc VGS 0 0.8 0.01
    alter VDS VDS_Val
    run
    let VDS_Val = VDS_Val + VDS_incr_step
end

** OUTPUT CHARACTERISTICS
dowhile VGS_Val <= VGS_Max
    dc VDS 0 0.8 0.01
    alter VGS VGS_Val
    run
    let VGS_Val = VGS_Val + VGS_incr_step
end

** ID vs VGS for different VDS
plot (-dc1.VDS#branch) (-dc2.VDS#branch) (-dc3.VDS#branch) (-dc4.VDS#branch) (-dc5.VDS#branch) (-dc6.VDS#branch) (-dc7.VDS#branch) (-dc8.VDS#branch) (-dc9.VDS#branch)

** ID vs VDS for different VGS
plot (-dc10.VDS#branch) (-dc11.VDS#branch) (-dc12.VDS#branch) (-dc13.VDS#branch) (-dc14.VDS#branch) (-dc15.VDS#branch) (-dc16.VDS#branch) (-dc17.VDS#branch) (-dc18.VDS#branch)
.endc

**** end user architecture code
**.ends
.GLOBAL GND
.end
```
</details>

| ![lab1_nfet_char_schematic_alterparam](/docs/images/lab1_nfet_char_schematic_alterparam.png) |
|:---:|

| ID vs. VDS | ID vs. VGS |
|:---:|:---|
| ![lab1_nfet_char_IdVds_alterparam](/docs/images/lab1_nfet_char_IdVds_alterparam.png) | ![lab1_nfet_char_IdVgs_alterparam](/docs/images/lab1_nfet_char_IdVgs_alterparam.png) |


#### 2.1.2 - Nested DC Sweep

### 2.2 - Module 2 Assignment - 7nm Inverter Characterization

#### 2.2.1 - Xschem schematic and SPICE directives for simulation

#### 2.2.2 - 

#### 2.2.3 - CMOS Inverter Characterization for different Wp, Wn (Lp = Ln = 7nm)
  - [Characterization Table PDF](./docs/pdf/Module2_Assignment_CMOSInverterChar.pdf)
  - Click on the image below to view graphs from the characterization data.  

  |[![asap7_CMOS_Inverter_char](/docs/images/Asgnmt_cmos_inverter_char_plots.png)](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arunkpv/vsd_asap7_workshop/refs/heads/main/docs/html/cmos_inv_plot.html)|
  |:---:|
  - **Observation needing closer look:** tPHL, tpLH going negative for some Wp/Wn ratios.

_________________________________________________________________________________________________________  

## 3 - Design of a BandGap Reference Circuit
### 3.1 - Theory: Design of a BGR Circuit
### 3.2 - Module 3 Assignent - Bandgap Reference Design and Simulation using Xschem
_________________________________________________________________________________________________________  

## Acknowledgements
 - Kunal Ghosh (VSD Corp. Pvt. Ltd.)
 - https://github.com/RSMadhuri66/Bandgap-Reference-Circuit-with-SCMB-with-ASAP-7nm-PDK-

