# Designing data intensive applications

## Building blocks

- persistent storage
- cache
- search index
- stream processing
- batch processing

# Reliability

- fault - system component deviating from normal behavior
- fault tolerance - continuous stress in production to prepare - Netflix chaos monkey
- fault prevention - security
- fault types
    - hardware
    - software
         - harder to cope with
         - slowdown
         - cascading failures
         - dormant bugs
         - countermeasures
              - process isolation
              - monitoring
              - alerting 
    - human
         - operator configuration error - bulk of problems
         - contermeasures
             - sandbox environment
             - thorugh testing
             - fast config rollback
             - gradual rollout
             - telemetry

# Scalability

- choose relevant load parameter
    - req/sec
    - read/write ratio
    - simultanious users
    - cache hit rate

- twitter (2012)
    - tweet post: 4.2k req/sec, 12k req/sec peak
    - timeline (fan out): 300k req/sec, difficult
         - strategy 1: on demand fill timeline for a user fetching data from followee
         - strategy 2: maintain cache for each user's timeline, during inserts find all followers and update their cache
         - strategy 3 (current): hybrid approach, heavily followed user's tweets handled with stratgy 1, everything else strategy 2
    - distribution of followers per user - key scalability parameter

- describing performance
    - measurement1: increase load parameter, analyse how is performance affected
    - measerement2: increase load parameter, what resources to increase to keep same performance

- latency vs response time
    - latency: request is latent, waiting for service
    - response time: network delay, queuing delay, service time

- metrics
    - in batch system - throughput
    - in online system - response time
    - mean doesn't tell how many users experience it
    - percentiles are better (medis = 50th percentil)
    - queueing delay is large part of high response time
        - head of line blocking - slow request "clogs" queue
        - client side response time measuring is vital
    - forward decay, t-digest, hdrhistorgram
    - historgram is the meaningful aggregation for response time (no percentile averaging)
    - averaging is usually not ideal: hides both typical and unusual behavior
    - it takes one slow call for the entire end-user request slowdown

- handling load
    - vertical scaling
    - horizontal scaling
    - shared nothing
    - elastic systems - adding resources based on monitoring

# Storage and retrieval

- Index
    - the structure derived from original data for faster queries
    - tradeoff - writes need to update the structure
 
- Hash index
    - example BitCask
      - in memory hash table keys with references to persistent file offset locations
      - values are 1 disk seek away and total data could be bigger than memory because of disk storage
      - the file could be in filesystem cache speeding up reads
      - append-only design
        - file writes of <key:newValue> type are done append only
        - sequential write operations are much faster than random writes (SSD included because erase before write)
        - concurrency and crash recovery is easier
        - compaction and segment merging keeps disk fragmentation low      
      - periodic log compaction is needed to compress file size
      - log is broken up into smaller segments
      - segment compaction example:
        - before compaction: <key1:oldValue>, <key2:someValue>, <key1:newValue>
        - after compaction: <key1:newValue>, <key2:someValue>
      - segments are never modified just deleted
      - new compacted segment is written to a new file
      - sequential segment hashmap search for keys is not costly, with frequent merging the number of segments is small
      - challenges
        - binary file format - with length prefix per record to avoid escaping
        - deletion - with a tombstone
        - crash recovery - snapshot needed for the in-memory hash table for fast boot
        - partial corrupted records - checksum needed for detection
        - concurrency control - one writer thread, multiple reader threads of immutable segments
        - cons
          - hash table should fit into memory
          - disk based hash map is slow, requires slow random IO
          - range queries are not efficient because keys are not ordered

- SSTables and LSM-trees
