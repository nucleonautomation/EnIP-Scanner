# EtherNet/IP Scanner - Quickstart

- Follow these steps to get started with the scanner:

### 1. Imports & Setup

```python
import Scanner
import os
import time
from random import randint as R
```

### 2. Callback Functions

```python
Running = False

Output_Data = {}

def Log():
    global Running
    if not Running:
        Running = True
        os.system('cls')
        for Key, Value in Output_Data.items():
            print(f"{Key}: {Value}")
        time.sleep(0.02)
        Running = False

def IO(Data):
    global Output_Data
    for Key, Value in Data.items():
        Run, IO = Value
        Output_Data[Key] = [Run, IO]
    Log()

def Config(Data):
    print(Data)
    
def Random_List(Length, Start, Stop):
    return [R(Start, Stop) for Index in range(Length)]
```

### 3. Scanner Configuration

```python
Scanner.Config(IP='192.168.17.17')
Scanner.Register('IO', IO)
Scanner.Register('Config', Config)
Scanner.Register('TCPIP', Config)
Scanner.Register('Ethernet', Config)
Scanner.Register('Reset', Config)
Scanner.Register('LED', Config)
Scanner.Register('LLDP', Config)
Scanner.Register('QoS', Config)
Scanner.Add(Name='A', IP='192.168.7.11', Datatype='UDINT', T_O_Length=4, O_T_Length=8, Config_Length=6, T_O_Instance=100, O_T_Instance=101, Config_Instance=102, T_O_RPI=100000, O_T_RPI=100000, Inhibit=False, T_O_Header=True, O_T_Header=True, Run=True, Unicast=False, Implicit=True, Connected_Explicit=True, Connected_Explicit_Time=1000)
Scanner.Add(Name='B', IP='192.168.7.11', Datatype='USINT', T_O_Length=64, O_T_Length=64, Config_Length=32, T_O_Instance=150, O_T_Instance=151, Config_Instance=152, T_O_RPI=200000, O_T_RPI=200000, Inhibit=False, T_O_Header=False, O_T_Header=False, Run=True)
```

### 4. Start & Runtime Loop

```python
Scanner.Start()

while True:
    time.sleep(0.01)
    Scanner.Write('A', Random_List(8, 31, 40))
    Scanner.Write('B', Random_List(64, 41, 50))
```
