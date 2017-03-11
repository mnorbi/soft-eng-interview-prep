# Numbers

- https://gist.github.com/jboner/2841832
- http://www.eecs.berkeley.edu/~rcs/research/interactive_latency.html

## Latency

### Memory

Operation  | Time    | Note
-----------|---------|----------------
L1 ref     | `1ns`   |
L2 ref     | `4ns`   |
RAM ref    | `100ns` |
SSD ref    | `16us`  | "Random read", eq. to HDD disk seek
HDD ref    | `4ms`   | Disk seek

### Read 1MB Sequentially

From | Time
-----|--------
RAM  | `20us`
SSD  | `300us`
HDD  | `20ms`

### CPU

Operation      | Time
---------------|-------
Mutex lock     | `17ns`
Compress 1KB   | `2us`
Context switch | `5us`

### Network

Operation             | Time
----------------------|--------
Send 2KB over network | `1us`
Round-trip in DC      | `500us`
Round-trip CA->AMS    | `150ms`


## Data Speeds

Operation      | Speed
---------------|------
HDD read/write | `150 MB/s`
SSD read/write | `500 MB/s`
USB 2.0 HDD    | `30 MB/s`
USB 3.0 SSD    | `300 MB/s`

## Time

Period | Seconds
-------|--------
1 hour | `3.6K`
1 day  | `86K`
1 year | `30M`

## Powers of 2

Power | Size          | Kibi
------|---------------|------
2^3   | 8             |
2^4   | 16            |
2^5   | 32            |
2^6   | 64            |
2^7   | 128           |
2^8   | 256           |
2^9   | 512           |
2^10  | 1024          | 1 `KiB`
2^16  | 65,536        |
2^20  | 1,048,576     | 1 `MiB`
2^30  | 1,073,741,824 | 1 `GiB`
2^32  | 4,294,967,296 |
2^40  | 1.09 * 10^12  | 1 `TiB`
2^64  | 1.84 * 10^19  |
