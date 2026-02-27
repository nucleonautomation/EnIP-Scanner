# EtherNet/IP Scanner Stack - Reference Manual

This manual documents the Setup API (Public functions) and the Event API (Callback data structures) for the EtherNet/IP Scanner Stack.

---

## 1. Setup API

These functions are used to configure the scanner before starting it, and to control it during runtime.

### Core Configuration

#### `Config(IP='0.0.0.0', Name=None, Callback_Pool=None, ...)`
Initializes the global device settings.
*   **IP** (`str`): The local IPv4 address to bind to.
*   **Name** (`str`): The network interface name (e.g., `'eth0'`). Auto-detected if `None`.
*   **Callback_Pool** (`int`): Number of threads reserved for IO callbacks.
*   **Vendor_Id** (`int`): Identity Object Attribute 1 (Default: 1).
*   **Product_Type** (`int`): Identity Object Attribute 2 (Default: 43).
*   **Product_Code** (`int`): Identity Object Attribute 3 (Default: 1).
*   **Revision_Major** (`int`): Identity Object Attribute 4 (Major).
*   **Revision_Minor** (`int`): Identity Object Attribute 4 (Minor).
*   **Product_Name** (`str`): Identity Object Attribute 7.
*   **Serial_Number** (`int`): Identity Object Attribute 6.

#### `Start()`
Starts the internal protocol threads (UDP I/O, TCP Explicit, Encapsulation, LLDP).
*   **Note**: Call this *after* `Config` and `Add`.

#### `Stop()`
Stops all protocol threads and closes network sockets.

#### `Register(Event, Callback)`
Registers a user function to handle specific events.
*   **Event** (`str`): One of `'IO'`, `'Assembly'`, `'TCPIP'`, `'Ethernet'`, `'LLDP'`, `'QoS'`, `'Reset'`, `'LED'`.
*   **Callback** (`func`): Function taking a single argument (`Data`).

---

### Scanner Management

#### `Add(Name, Datatype='USINT', ...)`
Defines a virtual adapter (I/O Assembly Set).
*   **Name** (`str`): Unique identifier.
*   **Datatype** (`str`): Element type (`'USINT'`,`'UINT'`,`'UDINT'`,`'SINT'`,`'INT'`,`'DINT'`,`'REAL'`).
*   **Inputs** (`int`): Size of Input Assembly (Produced TO PLC).
*   **Outputs** (`int`): Size of Output Assembly (Consumed FROM PLC).
*   **Configs** (`int`): Size of Configuration Assembly (BYTES Only).
*   **Input_Instance** (`int`): CIP Instance ID (Default: 100).
*   **Output_Instance** (`int`): CIP Instance ID (Default: 101).
*   **Config_Instance** (`int`): CIP Instance ID (Default: 102).
*   **T_O_RPI** / **O_T_RPI** (`int`): Default RPIs in microseconds.
*   **Inhibit** (`bool`): Start inhibited (Default: `False`).
*   **Receive_Header** / **Send_Header** (`bool`): Include 32-bit Run/Idle header in data.
*   **Unicast** (`bool`): Unicast connection (Default: `True`).
*   **Trigger** (`str`): Type of Implicit connection Trigger, Cyclic, COS (Default: `Cyclic`).
*   **Connection_Type** (`str`): Type of Implicit connection, Exclusive_Owner, Inpus_Only, Listen_Only (Default: `Exclusive_Owner`).
*   **Implicit** (`bool`): Implicit connection, other wise fallback to Explicit (Default: `True`).
*   **Connected_Explicit** (`bool`): Type of Explicit connection, Connected or Unconnected (Default: `True`).
*   **Connected_Explicit_Time** (`Int`): Explicited connection time in milliseconds to Read/Write data. False/0 will disable automatic Explicitr Read/Write (Default: `False`).

#### `Remove(Name)`
Removes an Adapter and closes its connections.

#### `Reset(Name)`
Clears an Adapter's data (sets to zero) and resets connections.

#### `Inhibit(Name, Value=None)` / `Run(Name, Value=None)`
Get or Set the Inhibit/Run status of an Adapter (True/False).

#### `Send_Header(Name, Value=None)` / `Receive_Header(Name, Value=None)`
Get or Set header handling configuration (True/False).

---

### Runtime Data Access

#### `Write(Name, Value, Index=None)`
Updates the Input (Produced) data sent to the PLC.
*   **Index** (`int` or `list`): Update specific index/indices. If `None`, updates full array.

#### `Read(Name, Index=None)`
Reads the latest Output (Consumed) data received from the PLC.

#### `Info(Adapter=None)`
Returns status and connection details for one or all Adapters.

#### `State(Value=None)`
Controls the Global Device State (Identity Object Status).
Returns current Global Device State.

---

### Object Attribute Helpers (Set/Get)

These functions modify specific attributes of CIP Objects.

**TCP/IP Interface (0xF5)**
*   `Get_Tcp_Interface()`, `Get_Tcp_Multicast()`, `Get_Tcp_Capabilities()`, `Get_Tcp_Status_Info()`, `Get_Tcp_Acd()`, `Get_Tcp_Quick_Connect()`, `Get_Tcp_Timeout()`
*   `Set_Tcp_Interface(Status, Control_Method, Control_DNS, Domain_Name, Hostname, Netmask, Gateway, Primary_Dns, Secondary_Dns)`
*   `Set_Tcp_Multicast(Alloc_Control, Num_Addrs, Start_Addr, TTL)`
*   `Set_Tcp_Capabilities(Bootp, Dhcp, Field_Settable, Hardware_Switch, Config_Settable, Acd)`
*   `Set_Tcp_Status_Info(Mcast_Config, Acd_Status)`
*   `Set_Tcp_Acd(Select, Activity, Remote_Mac, Hw_Type, Protocol, Hw_Size, Prot_Size, Opcode, Sender_Mac, Sender_Ip, Target_Mac, Target_Ip)`
*   `Set_Tcp_Quick_Connect(Enabled)`
*   `Set_Tcp_Timeout(Seconds)`

| Attribute Key | Description / Value Meaning |
| :--- | :--- |
| **Interface** | |
| `Status` | `0`=Unconfigured, `1`=Static IP, `2`=DHCP/BOOTP |
| `Mcast_Config` | `0`=Default Mcast, `1`=Custom Configured |
| `ACD_Status` | `0`=No Conflict, `1`=Conflict, `2`=Pending Detection |
| `Control_Method` | `0`=Load Static, `1`=Use BOOTP, `2`=Use DHCP |
| `Control_DNS` | `0`=DNS Disabled, `1`=DNS Enabled |
| `Safety_Network_Number` | List of 6 bytes (SNN for CIP Safety) |
| **Multicast** | |
| `TTL` | Time-to-Live for Multicast packets (Default=`1`) |
| `Alloc_Control` | `0`=Default algo, `1`=Specified by `Mcast_Start_Addr` |
| `Num_Addrs` | Number of Multicast addresses to allocate |
| `Start_Addr` | Starting Multicast IP address (Integer) |
| **Capabilities** | |
| `Bootp` | `1` = Hardware supports BOOTP Client |
| `Dhcp` | `1` = Hardware supports DHCP Client |
| `Field_Settable` | `1` = IP can be changed via CIP services |
| `Hardware_Switch` | `1` = IP can be changed via physical dials |
| `Config_Settable` | `1` = Config is stored in Non-Volatile Memory |
| `Acd` | `1` = Hardware supports Address Conflict Detection |
| **Status Info** | |
| `MCast_Config` | `0`=Default Mcast, `1`=Custom Configured |
| `Acd_Status` | `0`=No Conflict, `1`=Conflict, `2`=Pending Detection |
| **ACD (Address Conflict)** | |
| `Select` | `0`=Disable ACD, `1`=Enable ACD (Recommended) |
| `Activity` | `0`=None, `1`=Probing, `2`=Ongoing, `3`=Semi-Active |
| `Remote_MAC` | MAC address (`[0]*6`) of conflicting device |
| `Hw_Type` | Ethernet (Standard) |
| `Protocol` | IPv4 (Standard) |
| `Hw_Size` | MAC length in bytes |
| `Prot_Size` | IP length in bytes |
| `Opcode` | 1=Request, 2=Reply |
| `Sender_Mac` | MAC address (`[0]*6`) |
| `Sender_Ip` | IP address (`[0]*4`) |
| `Target_Mac` | MAC address (`[0]*6`) |
| `Target_Ip` | IP address (`[0]*4`) |
| **Quick Connect** | |
| `Enabled` | `0`=Disabled, `1`=Enabled (Fast boot for robotics) |
| **Timeout** | |
| `Seconds` | Seconds of silence before closing TCP socket Default 120 |

**Ethernet Link (0xF6)**
*   `Get_Ethernet_Capabilities()`, `Get_Ethernet_Status_Flags()`, `Get_Ethernet_Control()`, `Get_Ethernet_Link()`, `Get_Ethernet_States()`, `Get_Ethernet_Counters()`
*   `Set_Ethernet_Capabilities(Capability, Capability_Type)`
*   `Set_Ethernet_Status_Flags(Link_Status, Duplex_Status, Negotiation_Status, Manual_Reset, Hardware_Fault)`
*   `Set_Ethernet_Control(Auto_Negotiate, Duplex_Mode, Forced_Speed, Interface_Speed)`
*   `Set_Ethernet_Link(Interface_Label, Interface_ID, Interface_Type)`
*   `Set_Ethernet_States(Admin_State, Interface_State)`
*   `Clear_Ethernet_Counters()`

| Attribute Key | Description / Value Meaning |
| :--- | :--- |
| **Capabilities** | |
| `Capability` | Bitmask: `1`=Manual Speed/Duplex, `2`=Auto-negotiation, `3`=Auto-MDIX, `4`=Manual Pause |
| `Capability_Type` | List of [[Speed, Duplex]] options |
| **Status Flags** | |
| `Link_Status` | `0`=Down, `1`=Up |
| `Duplex_Status` | `0`=Half, `1`=Full |
| `Negotiation_Status` | `0`=In Progress, `3`=Success, `4`=Forced |
| `Manual_Reset` | `0`=Instant apply, `1`=Requires reset to apply settings |
| `Hardware_Fault` | `0`=OK, `1`=Internal chip failure |
| **Control** | |
| `Auto_Negotiate` | `0`=Manual, `1`=Auto |
| `Duplex_Mode` | `0`=Half, `1`=Full (Requested) |
| `Forced_Speed` | Speed in Mbps when Manual (10, 100, 1000) |
| `Interface_Speed` | Current actual operating speed in Mbps |
| **Link** | |
| `Interface_Name` | Adapter name (e.g., `'eth0'`) |
| `Interface_Label` | Physical name (e.g., `'Port 1'`) |
| `Interface_ID` | Interface ID (Integer) |
| `Interface_Type` | `1`=Internal, `2`=Twisted-pair (RJ45), `3`=Fiber |
| **States** | |
| `Admin_State` | `1`=Enable Port, `2`=Disable Port |
| `Interface_State` | `0`=Unknown, `1`=Operational, `2`=Disabled, `3`=Testing |

**LLDP Management (0x109)**
*   `Get_LLDP()`
*   `Set_LLDP(Enable_Length, Enable, Tx_Interval, Tx_Hold_Multiplier)`

| Attribute Key | Description / Value Meaning |
| :--- | :--- |
| **Configuration** | |
| `Enable_Length` | Number of ports/entries (Default=`1`) |
| `Enable` | List of Bools: `[Global, Port 1, ...]` (`0`=Disable, `1`=Enable) |
| `Tx_Interval` | Seconds between broadcasts (Default=`30`) |
| `Tx_Hold_Multiplier` | Multiplier for TTL (Default=`4`) |
| `Datastore_Handle` | Handle to internal DB (Default 0/Null) |
| `Last_Change_Time` | Time since last table change (Default 0) |

**QoS (0x48)**
*   `Get_QoS()`
*   `Set_QoS(Tag_Enable, DSCP_PTP_Event, DSCP_PTP_General, DSCP_Urgent, DSCP_Scheduled, DSCP_High, DSCP_Low, DSCP_Explicit)`

| Attribute Key | Description / Value Meaning |
| :--- | :--- |
| **Tagging** | |
| `Tag_Enable` | `0` = Don't use 802.1Q VLAN tags (Default) |
| **DSCP Values (0-63)** | |
| `DSCP_PTP_Event` | Priority for PTP Event frames (Highest, Default=`59`) |
| `DSCP_PTP_General` | Priority for PTP General frames (Default=`47`) |
| `DSCP_Urgent` | Priority for CIP Motion/Safety (Default=`55`) |
| `DSCP_Scheduled` | Priority for I/O Traffic / UDP (Default=`47`) |
| `DSCP_High` | Priority for High Priority Explicit (Default=`43`) |
| `DSCP_Low` | Priority for Low Priority Explicit (Default=`27`) |
| `DSCP_Explicit` | Priority for Standard Explicit / TCP (Default=`27`) |

---

### Data Utilities
*   `To_Bits(Value, Datatype)` / `From_Bits(Bits, Datatype)`
*   `Pack(Values, Datatype)` / `Unpack(Blob, Datatype)`

---

## 2. Event API (Callbacks)

When a registered event fires, your callback receives a specific dictionary.

### Event: `IO`
Triggered when Class 1 (Implicit) I/O is received over UDP and decoded into your Scanner’s **Consumed** data.

| Dictionary Key | Value Type | Description |
| :--- | :--- | :--- |
| `<ScannerName>` | `list` | A 2-element list: `[Run_Status, Data_List]` keyed by the Adapter name you used in `Add()`. |
| `Run_Status` | `bool \| None` | `True` = Run, `False` = Idle/Program, `None` = not provided (header not present / disabled). |
| `Data_List` | `list` | Decoded Output (O→T) payload values for that Adapter, as Python ints/floats depending on `Datatype`. |

Example Data: `{'A': [True, [10, 20, 30]]}`  
Behavior: Fired when valid cyclic data arrives for an established I/O connection (and not inhibited).

---

### Event: `TCPIP`
Triggered when a client sets attributes of the TCP/IP Interface Object (Class 0xF5, Instance 1).

**Important**: This event fires **only the changed attribute(s)** for the specific Set request (not the entire TCPIP object).

| Attribute ID | Attribute Name | Dictionary Key(s) | Value Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 3 | Configuration Control | `Control_Method` | `int` | `0`=Static, `1`=BOOTP, `2`=DHCP. |
| 5 | Interface Configuration | `IP_Addr`, `Netmask`, `Gateway`, `Primary_Dns`, `Secondary_Dns`, `Domain_Name` | `str` | Provided together on Attr 5 writes. |
| 6 | Host Name | `Hostname` | `str` | Hostname string. |
| 8 | TTL Value | `TTL_Value` | `int` | TTL; coerced to at least 1. |
| 9 | Multicast Config | `Mcast_Alloc_Control`, `Mcast_Num`, `Mcast_Start_Addr`, `Effective_Mcast_IP` | `int`/`str` | Start Addr is an integer; Effective_Mcast_IP is a computed string. |
| 10 | ACD Select | `Select_Acd` | `int` | ACD enable/disable flag. |
| 11 | Last Conflict / Clear | `Last_Conflict_Clear` | `int` | Fires when “clear ACD/last-conflict” is requested. |
| 12 | Quick Connect | `Quick_Connect` | `int` | Quick Connect enable/disable. |
| 13 | Inactivity Timeout | `Inactivity_Timeout` | `int` | Encapsulation inactivity timeout (seconds). |

Example Data: `{'Control_Method': 2}`  
Behavior: Use this to persist network settings or trigger a controlled restart if desired.

---

### Event: `Ethernet`
Triggered when a client sets attributes of the Ethernet Link Object (Class 0xF6, Instance 1).

**Important**: This event fires **only the changed field(s)** for the specific Set request.

| Attribute ID | Attribute Name | Dictionary Key(s) | Value Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 6 | Interface Control | `Forced_Speed`, `Auto_Negotiate` | `int` | Values come from the request payload; apply/link handling is up to your app/OS. |
| 9 | Admin State | `Admin_State` | `int` | `1`=Enable, `2`=Disable. |

Example Data: `{'Admin_State': 2}`  
Behavior: Use this to disable the port, force speed/duplex policy, or report “requires reset” behavior.

---

### Event: `LLDP`
Triggered when a client sets attributes of the LLDP Management Object (Class 0x109, Instance 1).

**Important**: This event fires **only the changed field(s)** for the specific Set request.

| Attribute ID | Attribute Name | Dictionary Key(s) | Value Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 1 | LLDP Enable (Struct) | `Enable_Length`, `Enable` | `int`, `list` | `Enable` is expanded into a list of 0/1 values (global/ports). |
| 2 | Message Tx Interval | `Tx_Interval` | `int` | Seconds; validated to be within allowed range. |
| 3 | Message Tx Hold | `Tx_Hold_Multiplier` | `int` | Validated range (e.g., 2..10). |
| 6 | Reinit Delay | `Reinit_Delay` | `int` | Validated range (e.g., 1..10). |

Example Data: `{'Tx_Interval': 30}`  
Behavior: Use this to update your LLDP UI/state; LLDP TX/RX threads consume these settings internally.

---

### Event: `QoS`
Triggered when a client sets attributes of the QoS Object (Class 0x48, Instance 1).

**Important**: This event fires the **full QoS dictionary copy** (not just a single changed key).

| Attribute ID | Attribute Name | Dictionary Key | Value Type | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Tag Enable | `Tag_Enable` | `int` | 802.1Q tagging enable/disable. |
| 2 | DSCP PTP Event | `DSCP_PTP_Event` | `int` | DSCP value (0..63). |
| 3 | DSCP PTP General | `DSCP_PTP_General` | `int` | DSCP value (0..63). |
| 4 | DSCP Urgent | `DSCP_Urgent` | `int` | DSCP value (0..63). |
| 5 | DSCP Scheduled | `DSCP_Scheduled` | `int` | DSCP value (0..63). |
| 6 | DSCP High | `DSCP_High` | `int` | DSCP value (0..63). |
| 7 | DSCP Low | `DSCP_Low` | `int` | DSCP value (0..63). |
| 8 | DSCP Explicit | `DSCP_Explicit` | `int` | DSCP value (0..63). |

Example Data: `{'Tag_Enable': 0, 'DSCP_PTP_Event': 59, 'DSCP_PTP_General': 47, 'DSCP_Urgent': 55, 'DSCP_Scheduled': 47, 'DSCP_High': 43, 'DSCP_Low': 27, 'DSCP_Explicit': 27}`  
Behavior: Use this event to update your own QoS config store; the stack may apply DSCP settings to sockets where supported.

---

### Event: `Reset`
Triggered by the Identity Object Reset Service (Service 0x05, Class 0x01, Instance 1).

| Dictionary Key | Value Type | Description |
| :--- | :--- | :--- |
| `Module` | `int` | Reset Type for “Module”-type reset requests (commonly `0`=Power Cycle, `1`=Factory Reset). |
| `Comm` | `int` | Reset Type for “Comm”-type reset requests (when reset type is not 0/1). |

Example Data: `{'Module': 1}` (Factory Reset requested)  
Behavior: The stack performs an internal hard reset, then fires this event so your application can clear stored settings and/or restart.

---

### Event: `LED`
Triggered by the Identity Object “Flash LED” Service (Service 0x4B, Class 0x01, Instance 1).

| Dictionary Key | Value Type | Description |
| :--- | :--- | :--- |
| `Duration` | `int` | Flash duration (typically seconds). If not provided, a default is used by the stack. |

Example Data: `{'Duration': 10}`  
Behavior: Use this event to blink a real LED (hardware) or show a visible identify indicator in your UI/app logs.