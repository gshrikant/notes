# USB 101
* "True" Plug and Play => Hot Plug
* One connector = Data + *Power*
* Auto-detect and configuration (discoverable).
* Three variants until USB 2.0:
    - Low Speed: 1.5 Mbps (v1.1, 2) [10-100 kbps]
    - Full Speed: 12 Mbps (v1.1, 2) [5-10 Mbps]
    - High Speed: 480 Mbps (v2) [25-400 Mbps]
    - Super Speed: v3
    - USB OTG
    * Real speeds not close to these due to software stack overhead.
* Lossless data transfers.
* In embedded:
    - Diagnostic data acquisition.
    - Service port (upgrade firmware).

## How is USB structured?
* Single host, multiple devices.
* *Tiered* star.
        HOST
      [Root Hub]
    ==============
     A    A    A
    ==============
    /     |     \
   /      |      \
  B       B       B
[Device] [2.0]  [1.1]
          A
          |
          B
      [1.1 Hub]
     A         A
     |         |
     |         |
     B         B
  [Device]  [Device]

* Upto 127 devices.
* Upto 7 tiers - up to 5 hubs deep with a max of 5 meters between hubs.
* Max. cable length between host controller and USB device = 25m
* *Hosts* control everything.
* Root hub dictates how many devices can be connected.
* Devices can be:
    1. Hubs
    2. Devices

### USB Host
    * Configures all devices.
    * Device enumeration.
    * Schedules bandwith and power.
### USB Hubs
    * Plugs into an upstream port and provides multiple downstream ports.
### USB Device
    * Strictly a *slave*.
    * Upto 16 endpoints.
        - 1 Bidirectional endpoint = 1 IN and 1 OUT.
### USB OTG
    * Handhelds, mostly.
    * Don't need a normal USB host and can communicate directly with each
    other (peer-to-peer).
    * Power is only 8mA.
    * OTG device = OTG host or OTG device.
    * 'True' OTG = Peer to Peer.

### Connectors
* A-connectors
    - Upstream
    - Host Port
    - Supplies power
* B-connector
    - Downstream
    - Device port

### Physical Layer and Power
    |===================|
    |GND D+ D- Vbus(Pwr)|
    |===================|
* 5V supply.
* 4 pins for typical USB (5 pins including ID pin for OTG).
* 2-wire *differential* signaling
    - 3.3 V for Low and Full Speeds (LS and FS).
    - 400 mV for HS.
* *NRZI* (Non Return to Zero Inverting) encoding with *bit stuffing*.
* Devices can be bus-powered or self-powered.
* High-power Device operating state = 500 mA (suspended state = 2.5 mA).

### USB Compliance
* 16-bit Vendor ID (VID) has to be assigned by USB-IF.

## USB Transfers
* Transfer types:
    1. Control (config and send commands)
    2. Bulk
    3. Isochronous
    4. Interrupt

### Control
    * Host SW request/response communication.
    * Used in *Enumerate* process.
    * Goes through Endpoint 0.
    * All devices must implement atleast 1 Control pipe on Endpoint 0.
    * Bidirectional transfers.
    * Burst and non-periodic.
### Bulk
    * Large amount of data.
    * Guaranteed delivery.
    * Packets = 8 to 512 bytes of data.
    * FS and HS devices.
    * High priority traffic gets more bandwidth. Bulk gets the leftovers
        - Bulk traffic fills in remaining gaps in the micro-frame.
        - For example, isochronous transfer prioritized over bulk transfers.
### Isochronous
    * Audio and Video streams.
    * No error checking. Lost packets acceptable.
    * Packets = 0 - 1024 bytes.
### Interrupt
    - Guaranteed latency.
    - Host polls the device periodically to check new data.
    - Mice, Joystick, Keyboard, Touchscreen.
* Examples:
1. Flash drive: Bulk *and* Control.
2. USB Camera: Isochronous and Control.
3. Touchscreen: Interrupt and Control.

## USB Frame
* Bus time is broken down into *frames* (1 ms) or micro-frames (125 us, only HS).
* *Host initiates all transactions*
* Peripherals share USB bandwidth through host-scheduled protocol.
* Token-based protocol used by host.
* Frame has to be scheduled with certain minimum timings.
* Frame = Start of Frame + Camera + Control + Touchscreen + Flash Drive.
* Different root-hub = different host-scheduling for frames = no bandwidth
limitation.
* Multiple isochronous devices = no single device allowed to hog the bus.

## Endpoints
* Uniquely addressable portion of the peripheral that is the source/receiver of
data (USB RAM buffer).
* Max. number of endpoints = 16
    - IN Endpoint = Host reads data
    - OUT Endpoint = Host writes data
* Allows isolation of command path from data path.
* Endpoints can be *grouped* to form a complex device which the OS treats as
COMPLETELY SEPARATE devices (ex. All-in-One printer).
* *Pipes* = Logical connections between endpoints on a device and host
controller's software i.e.
    Host Software <--------------> Endpoint
                        /|\
                         |
                        Pipe
* Pipes = Control and Data (Bulk/Isochronous/Interrupt).

### USB Transactions
* Transfer = Sum of (*Transactions*).
* Transfer = Process of carrying out a command from USB Host (ex. scan the paper)
* Transaction = Delivery of data to endpoint.
* Host splits transfer into multiple transactions by allocating transactions to
a USB frame.
* USB Frame = Several transactions.
* Transaction = Sum of (*Packets*)
* Packet = Block of information.
===========================================================================
| Packet = Sync | PID | Packet Specific Info | CRC | End of Packet (EOP)  |
===========================================================================
* USB Packets
1. Token Packet (Device Address + Endpoint#)
    - Header defining what to expect next (SOF/SETUP/IN/OUT).
2. Data Packet (0-1023 bytes payload).
    - Contains information.
3. Handshake Packet (ACK/NAK/STALL).

## USB Descriptors
* Used to identify and desribe device's capabilities.
* Stored in NV memory on USB device.
* Vendors can define their own descriptors.
* Standard descriptors:
    - Device, Configuration, Interface, Endpoint, String (optional)
### Device Descriptor
*Who are you? What driver should be loaded?*
    * Represents the entire device.
    * Supported USB version + Max. packet size + VID and PID + Configurations.
    * Ex: USB 2.0 + 64-byte max. packet size + VID + PID.
### Configuration Descriptor
*What resources do you need?*
    * Can have several different configurations, but only one can be enabled at
    a time.
    * Specifies: self power/bus-powered, max. power consumption, number of
    interfaces.
