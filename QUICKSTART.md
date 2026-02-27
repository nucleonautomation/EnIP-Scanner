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
Scanner.Register('Assembly', Config)
Scanner.Register('TCPIP', Config)
Scanner.Register('Ethernet', Config)
Scanner.Register('Reset', Config)
Scanner.Register('LED', Config)
Scanner.Register('LLDP', Config)
Scanner.Register('QoS', Config)
Scanner.Add(Name='A', Datatype='UDINT', Inputs=6, Outputs=6, Configs=6, Input_Instance=100, Output_Instance=101, Config_Instance=102, Inhibit=False, Receive_Header=True, Send_Header=True, Run=True, Unicast=False, Implicit=True, Connected_Explicit=True, Connected_Explicit_Time=1000)
Scanner.Add(Name='B', Datatype='USINT', Inputs=64, Outputs=64, Configs=32, Input_Instance=150, Output_Instance=151, Config_Instance=152, Inhibit=False, Receive_Header=False, Send_Header=False, Run=True))
```

### 4. Start & Runtime Loop

```python
Scanner.Start()

while True:
    time.sleep(0.01)
    Scanner.Write('A', Random_List(6, 1, 30))
    Scanner.Write('B', Random_List(64, 2, 60))
```
