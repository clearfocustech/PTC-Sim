# loco_sim

This application is a work in progress based on my experience in Postive Train Control. It demonstrates communication between active locomotives and a Back Office Server (BOS) and consists of three top-level parts:
  
**Locomotive Simulator**  
A simulated locomotive traveling on a track (as modeled by `track_rail.json`). As the loco travels, it connects to the radio base stations specified in `track_bases.json` for the purpose of communicating it's status (location, speed, etc.) to the BOS at regular intervals (defined in `conf.dat`). If a status would be transmitted while no base station is available, the locomotive does not send it's status at that time.  

The locomotive also watches for messages address to it in the messaging subsystem in order to receive commands (reduce speed, change direction, etc.) from the BOS. If the locomotive cannot be reached within the message Time-to-live (TTL), the message expires, thus avoiding receipt of stale commands.

Note: Multiple instances of the locomitive simulator may instantiated - each will give a simulated locomotive with a unique ID.

**Messaging Subsystem**  
Allows bi-directional communication between locomotives and the BOS by acting as a broker - messages from locomotives are enqued for receipt by the BOS, and messages to locomotives are enqued for the locomotive with the specified ID.

**Back Office Server**  
The BOS monitors the messaging subsystem and displays locomotive status graphically via its website and GoogleEarth. Additionally, the simulated locomotive may be controlled from the BOS.

## File Description

**conf.dat** - Configuration information. For example, the message broker hostname/IP address.  
**sim_bos.py** - The Back Office Server, or "BOS" (pronounced like "boss").  
**sim_broker.py** - The QPID message broker.  
**sim_lib.py** - Shared classes and helper functions.  
**sim_loco.py** - The locomotive simulator.  
**track_bases.json** - Specifies the base stations providing radio communication for sections of the track. Each base station consists of a unique ID and the on-track mileposts it provides coverage for. Gaps in coverage area allowed, as are areas of overlapping coverage.  
**track_rail.json** - A representation of a railroad track. Contains milepost markers and associated lat/long coordinates (in decimal degrees) of each.

## Message Specification

Adheres to EMP V4 (specified in msg_spec/S-9354.pdf) with fixed-format messages having a variable header section. This messaging implementation is defined as the following:

**EMP Fields Values**
|---------------------------------------------------|
| Section  | Field / Value                          |
|---------------------------------------------------|
| Common   | EMP Header Version    : 4              |
| Header   | Message Type/ID       : DYNAMIC        |
|          | Message Version       : 1              |
|          | Flags                 : 0              |
|          | Body Size             : DYNAMIC        |
|---------------------------------------------------|
| Optional | Unused                                 |
| Header   |                                        |
|---------------------------------------------------|
| Variable | Variable Header Size  : DYNAMIC        |
| Length   | Network Time to Live  : 120            |
| Header   | Quality of Service    : 0              |
|          | Sender Address        : DYNAMIC        |
|          | Destination Address   : DYNAMIC        |
|----------|----------------------------------------|
| Body     | Body/Data             : DYNAMIC        |
|          | CRC                   : DYNAMIC        |
|---------------------------------------------------|

**Fixed-Format Messages, by ID**
|-------------------------------------------------------|
| ID / Desc     | Data Element, by index                |
|-------------------------------------------------------|
| 6000:         | 0: A key/value string of the form     |
| Loco status   |    {sent          : Unix Time,         |
| message       |     loco          : 4 digit integer,   |
|               |     speed        : 2 digit integer,   |
|               |     lat     : Integer,           |
|               |     long    : Integer,           |
|               |     base : Integer            |
|               |    }                                  |
|-------------------------------------------------------|
| 6001:         | 0: A key/value string of the form     |
| BOS to loco   |    {sent    : Unix Time,         |
| command msg   |     loco : 4 digit integer,   |
|               |     cmd      : A well-formed cmd  |
|               |                    Ex: 'speed(55)'    |
|               |    }                                  |
|-------------------------------------------------------|

**Well-formed Command Strings**
speed(55), etc..

**Addressing**
sim.l.7357
sim.b

## Usage

From a terminal, start the BOS with `./demo.py`, then navigate to http://localhost/loco_sim.

## TODO
Logger output
check all docstrings for PEP8
Base station module


