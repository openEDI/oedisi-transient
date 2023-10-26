# oedisi-transient
Transient data simulated in ATP-EMTP
## Contents
The data is simulated through IEEE 123 bus system with 14 PVs in ATP-EMTP software. The ATP model is converted from an open-source model [IEEE123_PV.xml](https://github.com/GRIDAPPSD/CIMHub/blob/feature/SETO/OEDI/xml/IEEE123_PV.xml) via [CIMHub](https://github.com/GRIDAPPSD/CIMHub/tree/feature/SETO). The data format is in **CSV** or **COMTRADE**（.cfg and .dat) file.<br>
The data file label is named after the following rules:<br>
* Loading Condition: 0.4/1.0 ➡️ l1/l2<br>
* PV Capacity: 0.4/0.6/0.8/1.0 ➡️ c1/c2/c3/c4<br>
* Fault Location: ADJ1/197/450/82 ➡️ b1/b2/b3/b4<br>
* Fault Type: Three-phase/Single-phase/Line-to-line phase fault ➡️ f1/f2/f3<br>

*_The fault location defined in ATP model corresponds to the bus No. in XML file, which can be found in [IEEE123_PV.atpmap](https://github.com/yuqingdong0/Transient-Data-for-OEDI/blob/main/Simulation%20Data/IEEE%20123/Faults/IEEE123_PV.atpmap). In addition, ADJ1 is a fault location outside the feeder, approximately 250 feet away._<br>

## For COMTRADE files only
**Python Comtrade** is a module for Python 3 designed to read *Common Format for Transient Data Exchange* (**COMTRADE**) files. Detailed information can be found [here](https://github.com/dparrini/python-comtrade). These consists of oscillography data recorded during power system outages, control systems tests, validation and tests of field equipment, protective relaying logs, etc. The COMTRADE format is defined by IEEE Standards.
### Installation

```python
pip install comtrade
```

### Plot
The example below shows how to open CFG and DAT files to plot (using `pyplot`) analog channel oscillography. The **file_name** can be modified according to the file desired.

```python
import matplotlib.pyplot as plt
from comtrade import Comtrade

file_name = "ieee123_pv_l1c1b1f1"
rec = Comtrade()
rec.load(f"{file_name}.cfg", f"{file_name}.dat")
print("Trigger time = {}s".format(rec.trigger_time))

for i in range(rec.channels_count):
    plt.plot(rec.time, rec.analog[i])
    plt.legend([rec.analog_channel_ids[i]])
    plt.show()
```

## Results
There are 58 measured variables under each corresponding scenario in one data file.
* The variables which begin with _SBUS_ or _FAULT_ are system branch currents or node voltages, shown as below:

| Variable Name | Description | Variable Name | Description |
| :---: | :---: | :---: | :---: |
| SBUS A V-node | *Feederhead bus voltage in phase A* | SBUS C 25 C I-branch | *Feederhead current in phase C* |
| SBUS B V-node | *Feederhead bus voltage in phase B* | FAULTA I-branch | *Fault current in phase A* | 
| SBUS C V-node | *Feederhead bus voltage in phase C* | FAULTB I-branch | *Fault current in phase B* | 
| SBUS A 25 A I-branch | *Feederhead current in phase A* | FAULTC I-branch | *Fault current in phase C* | 
| SBUS B 25 B I-branch | *Feederhead current in phase B* | FALTBC 112 C I-branch | *Fault current in phase C of line to line fault* | 

* The variables which begin with _TACS_ section delimiter are inverter outputs, following the rules:
    * _PV###_ specifies the inverter number, from 1..999
    * suffix _V_ specifies the inverter's calculated RMS voltage (positive sequence in the case of three-phase); helpful to visualize the operation of undervoltage trip or ridethrough.
    * suffix _I_ specifies the inverter's RMS current injection (positive sequence in the case of three-phase); sufficient to visualize the status of this inverter. If the RMS current drops to zero, it means either a voltage or frequency trip function activated.
    * suffix _W_ specifies the inverter's estimated frequency (rad/s); helpful to diagnose grid synchronization issues.
    * suffixes _A_, _B_, _C_ specify the inverter's instantaneous phase voltages; needed to impute PMU data for this inverter.
    * suffixes _X_, _Y_, _Z_ specify the inverter's instantaneous injected currents; needed to impute PMU data for this inverter.


The fault is applied at 0.3s and never cleared. The time window is from 0.0s to 0.6s. Results of **SBUS A/B/C 25 A/B/C I-branch** in "ieee123_pv_l1c1b1f1" are shown below as examples. The units in *Y*-axis are *Ampere* (*A*).<br>

<img src="https://user-images.githubusercontent.com/113486786/205838702-e1ed48c9-12df-47ed-a4f7-42fc1b681617.png" width="600" height="300">
<img src="https://user-images.githubusercontent.com/113486786/205838721-df475388-3c5f-4ee6-ad7c-bcd782be61c1.png" width="600" height="300">
<img src="https://user-images.githubusercontent.com/113486786/205838745-1361b8d2-f49a-441f-9c2f-3a56611481af.png" width="600" height="300">
