# FinFET Circuit Design and Characterization
This GitHub repository documents the 10-day workshop on [FinFET Circuit Design and Characterization using ASAP7 PDK](https://www.vlsisystemdesign.com/7nm) offered by VSD Corp. Pvt. Ltd. attended from 27 Aug - 05 Sept, 2025.
<br/>

**Table of Contents**

 | Module # | Topic(s) Covered |
 |---|---|
 |[**Mod. 1**](#1---scaling-beyond-cmos-finfet-devices-and-innovations) | **Scaling Beyond CMOS: FinFET Devices and Innovations** <br> <ol> <li>[Path To Zetta Scale Computing](#11---path-to-zetta-scale-computing)</li> <li>[Introduction To FinFETs](#12---introduction-to-finfets)</li> <li>[FEOL Innovations](#13-feol-innovations)</li> <li>[BEOL Innovations](#14---beol-innovations)</li> </ol> |
 |[**Mod. 2**](#2---lab-to-simulation-7nm-finfet-inverter-performance-analysis) | **Lab-to-Simulation: 7nm FinFET Inverter Performance Analysis** <br> <ol> <li>[NFET DC Characteristics Using 7nm PDKs](#21---nfet-dc-characteristics-using-7nm-pdks)</li> <li>[First Inverter Characteristics Using 7nm FinFETs](#22---first-inverter-characteristics-using-7nm-finfets)</li><li>[Module 2 Assignment - 7nm Inverter Characterization](#23---module-2-assignment---7nm-inverter-characterization)</li> </ol> |
 |[**Mod. 3**](#3---design-of-a-bandgap-reference-circuit) | **Design of a BandGap Reference Circuit** <br> <ol> <li>[Theory: Design of a BGR Circuit](#31---theory-design-of-a-bgr-circuit)</li> <li>[Module 3 Assignent - Bandgap Reference Design and Simulation using Xschem](#32---module-3-assignent---bandgap-reference-design-and-simulation-using-xschem)</li> </ol> |

**NOTE: Theory part of the workshop needs to be updated in the documentation**
## 1 - Scaling Beyond CMOS: FinFET Devices and Innovations
### 1.1 - Path To Zetta Scale Computing

| ![Mod1_lecture_01c](/docs/images/lecture_screenshots/Mod1_lecture_01c.png) |
|:---:|

#### 1.1.1 - CMOS Evolution And Next-Gen Candidates

| ![Mod1_lecture_02](/docs/images/lecture_screenshots/Mod1_lecture_02.png) |
|:---:|

### 1.2 - Introduction To FinFETs

| ![Mod1_lecture_03](/docs/images/lecture_screenshots/Mod1_lecture_03.png) |
|:---:|
|![Mod1_lecture_04](/docs/images/lecture_screenshots/Mod1_lecture_04.png) |
| ![Mod1_lecture_05](/docs/images/lecture_screenshots/Mod1_lecture_05.png) |

### 1.3 FEOL Innovations
#### 1.3.1 - CMOS Technology Inflection Points

|  | **Dennard Scaling Era (~1 µm to 32 nm)** |
|:---|:---|
| ~1 µm (1980s) – 180 nm (1999) | Supply voltage scaling enabled higher integration (Dennard scaling) |
| 130 nm (2000) | Introduction of Cu BEOL (copper interconnects). |
| 90 nm (2003) | Uniaxial strained Si for mobility boost. |
| 65 nm (2005) | eSiGe + low-k dielectrics. |
| 45 nm (2007) | Transition to high-k / metal gate (HKMG) (first HK-first, MG-last). |
| 32 nm (2009) | HKMG + raised source/drain, improving leakage and variability. |
|  | **Post-Dennard / New Device Architectures (22 nm onward)** |
|  22 nm (2012) | Shift to FinFETs (Tri-Gate transistors) for leakage control |
| 14 nm (2014)/ 10 nm (2016) | Multiple patterning (SADP, SAQP, LELELE) to continue scaling |
| 7 nm (2018) | EUV lithography adoption. |
| 5 nm (2020) | SiGe FinFET PMOS with EUV for better performance. |
| 3/2/1.4 nm (2023-2025) | Transition to Gate-All-Around (GAA) nanosheet/ nano-wire FETs. |
| Sub-1 nm (future) | Exploration of CFET (stacked devices), 2D materials (e.g., MoS₂), and higher-k dielectrics for ultimate scaling. |


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
<details> <summary> <b>SPICE Deck:</b> nfet_char.spice </summary>

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

| ID vs. VDS |
|:---:|
| ![lab1_nfet_char_IdVds_alterparam](/docs/images/lab1_nfet_char_IdVds_alterparam.png) |
| **ID vs. VGS** |
| ![lab1_nfet_char_IdVgs_alterparam](/docs/images/lab1_nfet_char_IdVgs_alterparam.png) |

#### 2.1.2 - Nested DC Sweep
<details> <summary> <b>SPICE Deck:</b> nfet_char2.spice </summary>

```
** sch_path: /home/vsduser/Desktop/asap_7nm_Xschem/nfet_char2.sch
**.subckt nfet_char2
VGS G GND 0.7
VDS D GND 0.7
Xnfet1 D G GND GND asap_7nm_nfet l=7e-9 nfin=56
**** begin user architecture code

* ==========================================
* NFET DC Characterization
* ==========================================
.include ./asap7/asap7.spice

.control
pre_osdi ./asap7/bsimcmg.osdi
run
* =================================================
* 1) TRANSFER CHARACTERISTICS (Id vs Vgs for each Vds)
* First sweep is the inner loop (Vgs), second is the outer loop (Vds)
dc VGS 0 0.8 0.01 VDS 0 0.8 0.1
plot -i(VDS) vs v(G)

* =================================================
* 2) OUTPUT CHARACTERISTICS (Id vs Vds for each Vgs)
* First sweep is the inner loop (Vds), second is the outer loop (Vgs)
dc VDS 0 0.8 0.01 VGS 0 0.8 0.1
plot -i(VDS) vs v(D)
.endc

**** end user architecture code
**.ends
.GLOBAL GND
.end
```
</details>

| ID vs. VDS |
|:---:|
| ![lab1_nfet_char_IdVds_nested_DCSweep](/docs/images/lab1_nfet_char_IdVds_nested_DCSweep.png) |
| **ID vs. VGS** |
| ![lab1_nfet_char_IdVgs_nested_DCSweep](/docs/images/lab1_nfet_char_IdVgs_nested_DCSweep.png) |


### 2.2 - First Inverter Characteristics Using 7nm FinFETs

#### 2.2.1 - Xschem schematic and SPICE directives for evaluating performance metrics

| Xschem schematic |
|:---:|
| ![lab2_cmos_inv_schematic](/docs/images/lab2_cmos_inv_schematic.png) |

<details> <summary> <b>SPICE Deck:</b> inverter_vtc.spice <br> <u>Note:</u> <br> Char Loop hardcoded to only Nfin_P=14, Nfin_N=14 </summary>

```
** sch_path: /home/vsduser/Desktop/asap_7nm_Xschem/inverter_vtc.sch
**.subckt inverter_vtc
Vin Vin GND pulse({PULSE_VLO} {PULSE_VHI} 20p 10p 10p 40p 500p 1)
VDD VDD GND {VDD_V}
Xpfet1 Vout Vin VDD VDD asap_7nm_pfet l=7e-9 nfin={nfin_pmos}
Xnfet1 Vout Vin GND GND asap_7nm_nfet l=7e-9 nfin={nfin_nmos}

**** begin user architecture code
.include ./asap7/asap7.spice

.param nfin_pmos = 14
.param nfin_nmos = 14
.param VDD_V    = 0.7
.csparam VDD_V  = 'VDD_V'
.csparam VLOW   = '0.2 * VDD_V'
.csparam VMID   = '0.5 * VDD_V'
.csparam VHIGH  = '0.8 * VDD_V'

** Unique voltage offset added = 43.8mV
**    Sigma{ASCII(arun)}/10 = (97+114+117+110)/10 = 43.8mV
.param Vuniq = 0.0438

.param PULSE_VLO = '0 + Vuniq'
.param PULSE_VHI = 'VDD_V + Vuniq'

.temp 27

.control
    pre_osdi ./asap7/bsimcmg.osdi
    let nfin_typ    = 14

    let nfin_min    = nfin_typ
    let nfin_max    = nfin_typ
    let nfin_p = nfin_typ
    let nfin_n = nfin_typ

    echo "Nfin_P,Nfin_N,VM,Id_max,Av_max,vil,vih,vol,voh,NML,NMH,Gm_max,t_rise,t_fall,t_slew,f_slew_Hz,f_slew_GHz,tpHL,tpLH,t_pd,f_pd_Hz,f_pd_GHz,Id_peak_transient,energy_per_cycle,avg_power"  > cmos_inverter.csv

    dowhile nfin_p <= nfin_max
        let nfin_n = nfin_min
        alterparam nfin_nmos = $&nfin_n
        alterparam nfin_pmos = $&nfin_p
        dowhile nfin_n <= nfin_max
            echo "--------------------"
            echo "Nfin_P=$&nfin_p, Nfin_N=$&nfin_n"
            echo "--------------------"

            ** First run DC
            reset
            dc Vin 0 0.7 1m
            run
            plot Vout Vin

            *******************
            * DC measurements
            *******************
            * Switching Threshold (VM)
            meas DC VM FIND V(Vin) WHEN V(Vout)=V(Vin)

            * Drain Current (Id)
            let Id = VDD#branch
            plot Id
            meas DC Id_max MIN Id

            * Gain (Av)
            let gain_Av = abs(deriv(Vout))
            plot gain_Av
            meas DC Av_max MAX gain_Av

            * Vil, Vih, Vol, Voh, Noise Margin
            let dVout_dVin = deriv(Vout)
            meas DC vil FIND V(Vin) WHEN dVout_dVin=-1 cross=1
            meas DC voh FIND V(Vout) WHEN dVout_dVin=-1 cross=1
            meas DC vih FIND V(Vin) WHEN dVout_dVin=-1 cross=2
            meas DC vol FIND V(Vout) WHEN dVout_dVin=-1 cross=2
            let NML = vil - vol
            let NMH = voh - vih
            print NML
            print NMH

            * Transconductance, Gm
            let Gm = real(deriv(Id, Vin))
            plot Gm
            meas DC Gm_max MAX Gm

            * Output Resistance, Rout
            let R_out= deriv(Vout, Id)
            plot R_out

            shell sh -c "printf '%d,%d,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g' $&nfin_p $&nfin_n $&VM $&Id_max $&Av_max $&vil $&vih $&vol $&voh $&NML $&NMH $&Gm_max >> cmos_inverter.csv"

            *************************
            * Transient measurements
            *************************
            tran 1e-12 100e-12
            plot Vin Vout

            * Rise time, Fall time
            meas TRAN t_rise TRIG v(Vout) VAL=VLOW RISE=1 TARG v(Vout) VAL=VHIGH RISE=1
            meas TRAN t_fall TRIG v(Vout) VAL=VHIGH FALL=1 TARG v(Vout) VAL=VLOW FALL=1
            let t_slew      = t_rise + t_fall
            let t_slew_ps   = t_slew * 1e12
            let f_slew_Hz   = 1/ t_slew
            let f_slew_GHz  = f_slew_Hz/ 1e9
            print t_slew
            print f_slew_Hz

            ** Alternately,
            *
            * meas TRAN t1 WHEN v(Vout)=VLOW RISE=1
            * meas TRAN t2 WHEN v(Vout)=VHIGH RISE=1
            * let t_rise=(t2-t1)
            * print t_rise
            *
            * meas TRAN t3 WHEN v(Vout)=VHIGH FALL=1
            * meas TRAN t4 WHEN v(Vout)=VLOW FALL=1
            * let t_fall=(t4-t3)
            * print t_fall
            *

            * Propagation Delay, Frequency (prop. delay based)
            meas TRAN tpHL TRIG v(Vin) VAL=VMID RISE=1 TARG v(Vout) VAL=VMID FALL=1
            meas TRAN tpLH TRIG v(Vin) VAL=VMID FALL=1 TARG v(Vout) VAL=VMID RISE=1
            let t_pd        = (tpHL + tpLH)/ 2
            let t_pd_ps     = t_pd * 1e12
            let f_pd_Hz     = 1/ (2 * t_pd)
            let f_pd_GHz    = f_pd_Hz/ 1e9
            print t_pd
            print f_pd_Hz

            * Id, Power
            let Id_transient = VDD#branch
            plot Id_transient
            meas TRAN Id_peak_transient MIN Id_transient
            * Integral t1 to t2:
            *   t1 = start of pulse waveform (=PULSE_TD)
            *   t2 = end   of pulse waveform (= t1 + [tr+pw+tf])
            let t1 = 20p
            let t2 = t1 + (10p+40p+10p)
            meas TRAN Integral_Id INTEG Id_transient from={t1} to={t2}
            let energy_per_cycle = abs(Integral_Id * VDD_V)
            let avg_power = (energy_per_cycle / 60e-12)
            print energy_per_cycle
            print avg_power

            shell sh -c "printf ',%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g' $&t_rise $&t_fall $&t_slew $&f_slew_Hz $&f_slew_GHz $&tpHL $&tpLH $&t_pd $&f_pd_Hz $&f_pd_GHz $&Id_peak_transient $&energy_per_cycle $&avg_power >> cmos_inverter.csv"
            echo "" >> cmos_inverter.csv

            let nfin_n = nfin_n + 1
            alterparam nfin_nmos = $&nfin_n
        end
        let nfin_p = nfin_p + 1
    end

.endc

**** end user architecture code
**.ends
.GLOBAL GND
.GLOBAL VDD
.end
```
</details>

#### 2.2.2: Extracting Inverter Performance Metrics 

| Ngspice console output showing measurements |
|:---|
| ![lab2_cmos_inv_p14_n14_ngspice_log](/docs/images/lab2_cmos_inv_p14_n14_ngspice_log.png) |

**DC Analysis: VTC Curve (Vout vs. Vin)**
| ![lab2_cmos_inv_p14_n14_DC_Vout_vs_Vin](/docs/images/lab2_cmos_inv_p14_n14_DC_Vout_vs_Vin.png) |
|:---:|

```
** Switching Threshold (VM)
meas DC VM FIND V(Vin) WHEN V(Vout)=V(Vin)
```

**DC Analysis: Vil, Vol, Vol, Voh, Noise Margin**

```
** Vil, Vih, Vol, Voh, Noise Margin
let dVout_dVin = deriv(Vout)
meas DC vil FIND V(Vin) WHEN dVout_dVin=-1 cross=1
meas DC voh FIND V(Vout) WHEN dVout_dVin=-1 cross=1
meas DC vih FIND V(Vin) WHEN dVout_dVin=-1 cross=2
meas DC vol FIND V(Vout) WHEN dVout_dVin=-1 cross=2
let NML = vil - vol
let NMH = voh - vih
```

**DC Analysis: Drain Current (Id)**
| ![lab2_cmos_inv_p14_n14_DC_Id_vs_Vin](/docs/images/lab2_cmos_inv_p14_n14_DC_Id_vs_Vin.png) |
|:---:|

```
** Drain Current (Id)
let Id = VDD#branch
plot Id
meas DC Id_max MIN Id
```

**DC Analysis: Gain (Av)**
| ![lab2_cmos_inv_p14_n14_DC_GainAv_vs_Vin](/docs/images/lab2_cmos_inv_p14_n14_DC_GainAv_vs_Vin.png) |
|:---:|

```
** Gain (Av)
let gain_Av = abs(deriv(Vout))
plot gain_Av
meas DC Av_max MAX gain_Av
```

**DC Analysis: Transconductance (Gm)**
| ![lab2_cmos_inv_p14_n14_DC_Gm_vs_Vin](/docs/images/lab2_cmos_inv_p14_n14_DC_Gm_vs_Vin.png) |
|:---:|

```
** Transconductance, Gm
let Gm = real(deriv(Id, Vin))
plot Gm
meas DC Gm_max MAX Gm
```

**DC Analysis: Output Resistance (Rout)**
| ![lab2_cmos_inv_p14_n14_DC_Rout_vs_Vin](/docs/images/lab2_cmos_inv_p14_n14_DC_Rout_vs_Vin.png) |
|:---:|

```
*** Output Resistance, Rout
let R_out= deriv(Vout, Id)
plot R_out
```

**TRANsient Analysis: Vin, Vout waveforms**
| ![lab2_cmos_inv_p14_n14_TRAN_Vout_Vin_vs_t](/docs/images/lab2_cmos_inv_p14_n14_TRAN_Vout_Vin_vs_t.png) |
|:---:|

**TRANsient Analysis: Rise time, Fall time, Frequency (f_slew)**
```
.param VDD_V    = 0.7
.csparam VDD_V  = 'VDD_V'
.csparam VLOW   = '0.2 * VDD_V'
.csparam VHIGH  = '0.8 * VDD_V'

** Rise time, Fall time, f_slew
meas TRAN t_rise TRIG v(Vout) VAL=VLOW RISE=1 TARG v(Vout) VAL=VHIGH RISE=1
meas TRAN t_fall TRIG v(Vout) VAL=VHIGH FALL=1 TARG v(Vout) VAL=VLOW FALL=1
let t_slew      = t_rise + t_fall
let f_slew_Hz   = 1/ t_slew
```

**TRANsient Analysis: Propagation Delay, Frequency (f_pd)**
```
.csparam VMID   = '0.5 * VDD_V'

** Propagation Delay, Frequency (f_pd)
meas TRAN tpHL TRIG v(Vin) VAL=VMID RISE=1 TARG v(Vout) VAL=VMID FALL=1
meas TRAN tpLH TRIG v(Vin) VAL=VMID FALL=1 TARG v(Vout) VAL=VMID RISE=1
let t_pd        = (tpHL + tpLH)/ 2
let f_pd_Hz     = 1/ (2 * t_pd)
```

**TRANsient Analysis: Switching Energy, Avg. Power**
| ![lab2_cmos_inv_p14_n14_TRAN_Id_vs_t](/docs/images/lab2_cmos_inv_p14_n14_TRAN_Id_vs_t.png) |
|:---:|

```
** Trasient Drain current, Id
let Id_transient = VDD#branch
plot Id_transient
meas TRAN Id_peak_transient MIN Id_transient

** Total Energy, Avg. Power per cycle
* Integral t1 to t2:
*   t1 = start of pulse waveform (=PULSE_TD)
*   t2 = end   of pulse waveform (= t1 + [tr+pw+tf])
let t1 = 20p
let t2 = t1 + (10p+40p+10p)
meas TRAN Integral_Id INTEG Id_transient from={t1} to={t2}
let energy_per_cycle = abs(Integral_Id * VDD_V)
let avg_power = (energy_per_cycle / 60e-12)
```

### 2.3 - Module 2 Assignment - 7nm Inverter Characterization

**7nm FINFET CMOS Inverter Characterization for different Wp, Wn (Lp = Ln = 7nm)**

<details> <summary> <b>SPICE Deck:</b> inverter_char.spice <br> <u>Notes:</u> <br> 1) Char Loop for Nfin_P=7-21, Nfin_N=7-21 <br> 2) Also, plots are commented out in below shmoo due to the large set of points. <br>   (VTC, Id, Delay etc. plots for Nfin_P/N=14 were already provided in the preceeding section)</summary>

```
** sch_path: /home/vsduser/Desktop/asap_7nm_Xschem/inverter_char.sch
**.subckt inverter_char
Vin Vin GND pulse({PULSE_VLO} {PULSE_VHI} 20p 10p 10p 40p 500p 1)
VDD VDD GND {VDD_V}
Xpfet1 Vout Vin VDD VDD asap_7nm_pfet l=7e-9 nfin={nfin_pmos}
Xnfet1 Vout Vin GND GND asap_7nm_nfet l=7e-9 nfin={nfin_nmos}

**** begin user architecture code
.include ./asap7/asap7.spice

.param nfin_pmos = 14
.param nfin_nmos = 14
.param VDD_V    = 0.7
.csparam VDD_V  = 'VDD_V'
.csparam VLOW   = '0.2 * VDD_V'
.csparam VMID   = '0.5 * VDD_V'
.csparam VHIGH  = '0.8 * VDD_V'

** Unique voltage offset added = 43.8mV
**    Sigma{ASCII(arun)}/10 = (97+114+117+110)/10 = 43.8mV
.param Vuniq = 0.0438

.param PULSE_VLO = '0 + Vuniq'
.param PULSE_VHI = 'VDD_V + Vuniq'

.temp 27

.control
    pre_osdi ./asap7/bsimcmg.osdi
    let nfin_typ    = 14
    let nfin_min    = nfin_typ - (nfin_typ/ 2)
    let nfin_max    = nfin_typ + (nfin_typ/ 2)

    echo "Nfin_P,Nfin_N,VM,Id_max,Av_max,vil,vih,vol,voh,NML,NMH,Gm_max,t_rise,t_fall,t_slew,f_slew_Hz,f_slew_GHz,tpHL,tpLH,t_pd,f_pd_Hz,f_pd_GHz,Id_peak_transient,energy_per_cycle,avg_power"  > cmos_inverter.csv

    let nfin_p = nfin_min

    dowhile nfin_p <= nfin_max
        let nfin_n = nfin_min
        alterparam nfin_pmos = $&nfin_p
        alterparam nfin_nmos = $&nfin_n
        dowhile nfin_n <= nfin_max
            echo "--------------------"
            echo "Nfin_P=$&nfin_p, Nfin_N=$&nfin_n"
            echo "--------------------"

            ** First run DC
            reset
            dc Vin 0 0.7 1m
            run
            *plot Vout Vin

            *******************
            * DC measurements
            *******************
            * Switching Threshold (VM)
            meas DC VM FIND V(Vin) WHEN V(Vout)=V(Vin)

            * Drain Current (Id)
            let Id = VDD#branch
            *plot Id
            meas DC Id_max MIN Id

            * Gain (Av)
            let gain_Av = abs(deriv(Vout))
            *plot gain_Av
            meas DC Av_max MAX gain_Av

            * Vil, Vih, Vol, Voh, Noise Margin
            let dVout_dVin = deriv(Vout)
            meas DC vil FIND V(Vin) WHEN dVout_dVin=-1 cross=1
            meas DC voh FIND V(Vout) WHEN dVout_dVin=-1 cross=1
            meas DC vih FIND V(Vin) WHEN dVout_dVin=-1 cross=2
            meas DC vol FIND V(Vout) WHEN dVout_dVin=-1 cross=2
            let NML = vil - vol
            let NMH = voh - vih
            print NML
            print NMH

            * Transconductance, Gm
            let Gm = real(deriv(Id, Vin))
            *plot Gm
            meas DC Gm_max MAX Gm

            * Output Resistance, Rout
            let R_out= deriv(Vout, Id)
            *plot R_out

            shell sh -c "printf '%d,%d,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g' $&nfin_p $&nfin_n $&VM $&Id_max $&Av_max $&vil $&vih $&vol $&voh $&NML $&NMH $&Gm_max >> cmos_inverter.csv"

            *************************
            * Transient measurements
            *************************
            tran 1e-12 100e-12
            *plot Vin Vout

            * Rise time, Fall time
            meas TRAN t_rise TRIG v(Vout) VAL=VLOW RISE=1 TARG v(Vout) VAL=VHIGH RISE=1
            meas TRAN t_fall TRIG v(Vout) VAL=VHIGH FALL=1 TARG v(Vout) VAL=VLOW FALL=1
            let t_slew      = t_rise + t_fall
            let t_slew_ps   = t_slew * 1e12
            let f_slew_Hz   = 1/ t_slew
            let f_slew_GHz  = f_slew_Hz/ 1e9
            print t_slew
            print f_slew_Hz

            ** Alternately,
            *
            * meas TRAN t1 WHEN v(Vout)=VLOW RISE=1
            * meas TRAN t2 WHEN v(Vout)=VHIGH RISE=1
            * let t_rise=(t2-t1)
            * print t_rise
            *
            * meas TRAN t3 WHEN v(Vout)=VHIGH FALL=1
            * meas TRAN t4 WHEN v(Vout)=VLOW FALL=1
            * let t_fall=(t4-t3)
            * print t_fall
            *

            * Propagation Delay, Frequency (prop. delay based)
            meas TRAN tpHL TRIG v(Vin) VAL=VMID RISE=1 TARG v(Vout) VAL=VMID FALL=1
            meas TRAN tpLH TRIG v(Vin) VAL=VMID FALL=1 TARG v(Vout) VAL=VMID RISE=1
            let t_pd        = (tpHL + tpLH)/ 2
            let t_pd_ps     = t_pd * 1e12
            let f_pd_Hz     = 1/ (2 * t_pd)
            let f_pd_GHz    = f_pd_Hz/ 1e9
            print t_pd
            print f_pd_Hz

            * Id, Power
            let Id_transient = VDD#branch
            *plot Id_transient
            meas TRAN Id_peak_transient MIN Id_transient
            * Integral t1 to t2:
            *   t1 = start of pulse waveform (=PULSE_TD)
            *   t2 = end   of pulse waveform (= t1 + [tr+pw+tf])
            let t1 = 20p
            let t2 = t1 + (10p+40p+10p)
            meas TRAN Integral_Id INTEG Id_transient from={t1} to={t2}
            let energy_per_cycle = abs(Integral_Id * VDD_V)
            let avg_power = (energy_per_cycle / 60e-12)
            print energy_per_cycle
            print avg_power

            shell sh -c "printf ',%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g,%.4g' $&t_rise $&t_fall $&t_slew $&f_slew_Hz $&f_slew_GHz $&tpHL $&tpLH $&t_pd $&f_pd_Hz $&f_pd_GHz $&Id_peak_transient $&energy_per_cycle $&avg_power >> cmos_inverter.csv"
            echo "" >> cmos_inverter.csv

            let nfin_n = nfin_n + 1
            alterparam nfin_nmos = $&nfin_n
        end
        let nfin_p = nfin_p + 1
    end

.endc

**** end user architecture code
**.ends
.GLOBAL GND
.GLOBAL VDD
.end
```
</details>

<br>

**Characterization Results:**
  - [Characterization Table PDF](./docs/pdf/Module2_Assignment_CMOSInverterChar.pdf)
  - Click on the image below to view graphs from the characterization data.  

  |[![asap7_CMOS_Inverter_char](/docs/images/Asgnmt_cmos_inverter_char_plots.png)](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arunkpv/vsd_asap7_workshop/refs/heads/main/docs/html/cmos_inv_plot.html)|
  |:---:|
  - _**Observation needing closer look:**_ tPHL, tpLH going negative for some Wp/Wn ratios.

_________________________________________________________________________________________________________  

## 3 - Design of a BandGap Reference Circuit
### 3.1 - Theory: Design of a BGR Circuit
### 3.2 - Module 3 Assignent - Bandgap Reference Design and Simulation using Xschem

Circuit Diagram based on which the schematic is drawn:  

| ![bgr_ckt_diagram_base](/docs/images/bgr_ckt_diagram_base.png) |
|:---|
| _**Ref:**_ <br> 1. [Bandgap-Reference-Circuit-with-SBCM-with-ASAP-7nm-PDK](https://github.com/RSMadhuri66/Bandgap-Reference-Circuit-with-SCMB-with-ASAP-7nm-PDK-?tab=readme-ov-file#step-by-step-design-overview) <br> 2. [VSD Open 2021 BGR](https://github.com/vsdip/vsdopen2021_bgr?tab=readme-ov-file#331-final-circuit) |

| BGR Schematic in Xschem showing Runiq |
|:---|
| ![lab3_bgref_xschem_schematic](/docs/images/lab3_bgref_xschem_schematic.png) |

<details> <summary> <b>SPICE Deck:</b> bgr_dc.spice <br> </summary>

```
** sch_path: /home/vsduser/Desktop/asap_7nm_Xschem/bgr_dc.sch
**.subckt bgr_dc
Xpfet1 net6 net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet2 net1 net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet3 VREF net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet4 net2 net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet5 net3 net1 net2 VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet6 net6 net3 net1 VDD asap_7nm_pfet l=7e-9 nfin=14
Xnfet1 net6 net6 net7 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet2 net7 net7 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet3 net1 net6 R1.1 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet4 net3 net3 net4 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet5 net4 net4 net5 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet6 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet7 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet8 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet9 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet10 VCTAT VCTAT GND GND asap_7nm_nfet l=7e-9 nfin=14
R1 R1.1 R1.2 33k m=1
R2 VREF VCTAT 50k m=1
VDD VDD GND 1
R3 net5 GND 438 m=1

**** begin user architecture code
.include ./asap7/asap7.spice

.dc temp -45 150 1

.control
pre_osdi ./asap7/bsimcmg.osdi
run

let VPTAT = V(R1.1)-V(R1.2)
let dVREF_dT = deriv(V(VREF)) * 1e6

plot VPTAT
plot V(VCTAT)
plot V(VREF)
plot dVREF_dT

meas DC VPTAT_Max MAX VPTAT
meas DC VPTAT_Min MIN VPTAT

meas DC VCTAT_Max MAX V(VCTAT)
meas DC VCTAT_Min MIN V(VCTAT)

meas DC VREF_Min MIN V(VREF)
meas DC VREF_Max MAX V(VREF)
meas DC T0 WHEN V(VREF)=VREF_Max

let VPTAT_Slope = (VPTAT_Max - VPTAT_Min)/ 195
let VCTAT_Slope = (VCTAT_Min - VCTAT_Max)/ 195
print VPTAT_Slope
print VCTAT_Slope
.endc

**** end user architecture code
**.ends
.GLOBAL VDD
.GLOBAL GND
.end
```
</details>

**DC Analysis: Temperature Sweep Results**
| Ngspice Console Log <br> ![lab3_bgref_DC_ngspice_console_log](/docs/images/lab3_bgref_DC_ngspice_console_log.png) |
|:---|

| **VCTAT** <br> ![lab3_bgref_DC_VCTAT](/docs/images/lab3_bgref_DC_VCTAT.png) | **VPTAT** <br> ![lab3_bgref_DC_VPTAT](/docs/images/lab3_bgref_DC_VPTAT.png) |
|:---:|:---:|
| **VREF** <br> ![lab3_bgref_DC_VREF](/docs/images/lab3_bgref_DC_VREF.png) | **d(VREF)/dT** <br> ![lab3_bgref_DC_d(VREF)_dT](/docs/images/lab3_bgref_DC_d(VREF)_dT.png) |


**TRANsient Analysis (VDD = 1V, Temp = 27C)**
<details> <summary> <b>SPICE Deck for TRANsient Analysis:</b> </summary>

```
** sch_path: /home/vsduser/Desktop/asap_7nm_Xschem/bgr_tran.sch
**.subckt bgr_tran
Xpfet1 net6 net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet2 net1 net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet3 VREF net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet4 net2 net1 VDD VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet5 net3 net1 net2 VDD asap_7nm_pfet l=7e-9 nfin=14
Xpfet6 net6 net3 net1 VDD asap_7nm_pfet l=7e-9 nfin=14
Xnfet1 net6 net6 net7 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet2 net7 net7 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet3 net1 net6 R1.1 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet4 net3 net3 net4 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet5 net4 net4 net5 GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet6 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet7 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet8 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet9 R1.2 R1.2 GND GND asap_7nm_nfet l=7e-9 nfin=14
Xnfet10 VCTAT VCTAT GND GND asap_7nm_nfet l=7e-9 nfin=14
R1 R1.1 R1.2 33k m=1
R2 VREF VCTAT 50k m=1
VDD VDD GND 1
R3 net5 GND 438 m=1

**** begin user architecture code
.include ./asap7/asap7.spice

.temp 27
**.ic V(VDD)=0 V(net1)=0 V(net3)=0 V(net6)=0
.tran 1p 20n uic

.control
pre_osdi ./asap7/bsimcmg.osdi
run

let VPTAT = V(R1.1) - V(R1.2)
plot VPTAT xlimit 0 80p
plot V(VCTAT) xlimit 0 80p
plot V(VREF) xlimit 0 80p

meas tran VREF_Final FIND V(VREF) AT=20n
let VREF_TOLERANCE = 1/100
let VREF_Final_TOL = (1 - VREF_TOLERANCE) * VREF_Final
meas tran startup_time WHEN V(VREF)=VREF_Final_TOL RISE=1
print startup_time
.endc

**** end user architecture code
**.ends
.GLOBAL VDD
.GLOBAL GND
.end
```
</details>

| Ngspice Console Log |
|:---|
| ![lab3_bgref_TRAN_ngspice_console_log](/docs/images/lab3_bgref_TRAN_ngspice_console_log.png) |

| **VREF** <br> ![lab3_bgref_TRAN_Vref](/docs/images/lab3_bgref_TRAN_Vref.png) | |
|:---:|:---:|
| **VPTAT** <br> ![lab3_bgref_TRAN_VPTAT](/docs/images/lab3_bgref_TRAN_VPTAT.png) | **VCTAT** <br> ![lab3_bgref_TRAN_VCTAT](/docs/images/lab3_bgref_TRAN_VCTAT.png) |

**<u>BGRef Characterization Table</u>**

_Note:_
  - We haven't performed the actual proper design of the BandGap Vref circuit in ASAP7, in the sense that we haven't calculated the bias currents & bias voltages, transistor sizing, resistor values etc.
  - The reference repo also does not contain any design details/ calculations in this regard
  - Also, we haven't extracted the required nMOS & pMOS characteristics for the ASAP7 process to be used in the calculations
  (All we have from the nMOS char done in lab 1 are the nMOS VTH at around 0.18V and Drain current ranges in saturation for a 7nm long, 14 fin Finfet)

  - So what we have done instead (as in the reference repo) is to replace all MOSFETs in the [base circuit diagram](/docs/images/bgr_ckt_diagram_base.png) by n/pMOS with [L=7nm, W=14 fins], and BJTs by diode connected NMOSes.
    It is still doubtful to me whether a diode-connected NMOS can be used as a replacement for a diode-connected BJT to function as a proper CTAT. Even if it could be used that way, we haven't done the necessary calculations to fix their operating point in saturation (I=V^2) or sub-threshold (I=e^V)
  Hence, we are left to figure out the operating points, range of resistor values and even the range of allowed VDD values by trial and error!
  - So without all the above, the characterization data of the BG Reference in the below table is only for representation purposes and not of any practical value.

![lab3_bgref_Char_Table](/docs/images/lab3_bgref_Char_Table.png)

_________________________________________________________________________________________________________  

## References:
 - Kunal Ghosh (VSD Corp. Pvt. Ltd.)
 - https://github.com/RSMadhuri66/Bandgap-Reference-Circuit-with-SCMB-with-ASAP-7nm-PDK-
 - https://github.com/vsdip/vsdopen2021_bgr




_________________________________________________________________________________________________________  

## Miscellaneous Notes:

1. Interestingly, Ngspice's SPICE engine does not seem to support `.step param`. Instead `alter` or `alterparam` inside a loop. See: [Section 12.14.4.3 in Ngspice Manual](https://ngspice.sourceforge.io/docs/ngspice-html-manual/manual.xhtml#magicparlabel-24363)
2. It looks like in Ngspice, we can only plot currents through (independent) voltage sources. It is possible to put i(Vx), but not i(Rx),. etc. To workaround this, we can add a 0V source in whatever branch of the circuit we want to plot the current through (say, Vdummy) and put plot i(Vdummy)
3. [ST AN2386 - Threshold voltage thermal coefficient of the MOSFET](https://www.st.com/resource/en/application_note/an2386-how-to-achieve-the-threshold-voltage-thermal-coefficient-of-the-mosfet-acting-on-design-parameters-stmicroelectronics.pdf)