This is not a discussion on what the CAP Theorem is, but on the tradeoffs that GUN decides to default to.

### AP

GUN is an AP system by default, this means GUN can successfully read and write data even if other peers are offline. As a result, GUN is not strongly consistent in linearizable ways. Instead, it is eventually consistent in order to be highly available to users regardless of connectivity. When connectivity is or becomes available again, GUN will synchronize data at low latency with a guarantee all peers will deterministically converge to the same data within a time frame, without any extra coordination or gossip.

... work in progress ...