chronosdb
=========

Scalable datastore for metrics, events, and real-time analytics

Requirements
------------

* horizontal scalable
* http interface
* udp interface (low priority)
* persistent
* metadata for time series
* perform functions quickly (count, unique, sum, etc.)
* group by time intervals (e.g. count ticks every 5 minutes)
* joining multiple time series to generate new timeseries
* dynamic schema
* filter/query language (sql subset) with where clauses
* support multiple databases with read/write api key
* single time series should scale horizontally (no hot spots)
* dynamic cluster changes and data balancing
* pubsub layer
* continuous queries (keep connection open and return new points as they arrive)
* Delete ranges of points from any number of timeseries (that should reflect in disk space usage)
* querying should support one or more timeseries (possibly with regex to match on)

New Requirements
----------------
* Easy to backup and restore
* Large time range queries with one column ?
* Optimize for HDD access ?
* What are the common use cases that we should optimize for ?

Modules
-------


           +--------------------+   +--------------------+
           |                    |   |                    |
           |  WebConsole/docs   |   |      Http API      |
           |                    |   |                    |
           +------------------+-+   +-+------------------+
                              |       |
                              |       |
                        +-----+-------+-----------+
                        |                         |
                        |  Lang. Bindings         |
                        |                         |
                        +-----------------+       |
                        |                 |       |
                        |   Query Engine  |       |
                        |                 |       |
                        +-----------------+-------+
                        |                         |
                        |  Processing Engine      |
                        |                         |
                        +-------------------------+
                        |                         |
                   +----+ Coordinator (consensus) +-----+
                   |    |                         |     |
                   |    +-------------------------+     |
                   |                                    |
                   |                                    |
          +--------+-----------+                +-------+------------+
          |                    |                |                    |
          |   Storage Engine   |                |   Storage Engine   |
          |                    |                |                    |
          +--------+-----------+                +-------+------------+

Concensus Notes
---------------

Two state machines:
* 1 for the entire cluster of which machines are taking which portions of the ring
* 1 for each portion of the ring to replicate the operations
sequence number per ring location? that's the concensus, if they don't agree then request a replay from the last known sequence number
