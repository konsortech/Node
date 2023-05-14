## Restart 1 - Sun May 14 2023 5:03PM UTC

```
service humansd restart && journalctl -fu humansd -o cat

humansd.service: Deactivated successfully.
Stopped humans.
humansd.service: Consumed 10h 34min 57.075s CPU time.
Started humans.
5:03PM INF Unlocking keyring
5:03PM INF starting ABCI with Tendermint
5:03PM INF starting node with ABCI Tendermint in-process
5:03PM INF service start impl=multiAppConn module=proxy msg={} server=node
5:03PM INF service start connection=query impl=localClient module=abci-client msg={} server=node
5:03PM INF service start connection=snapshot impl=localClient module=abci-client msg={} server=node
5:03PM INF service start connection=mempool impl=localClient module=abci-client msg={} server=node
5:03PM INF service start connection=consensus impl=localClient module=abci-client msg={} server=node
5:03PM INF service start impl=EventBus module=events msg={} server=node
5:03PM INF service start impl=PubSub module=pubsub msg={} server=node
5:03PM INF service start impl=IndexerService module=txindex msg={} server=node
5:03PM INF ABCI Handshake App Info hash="�6;�?������\x0f4�G$f}u����\x06h��ڀ\x17��" height=47168 module=consensus protocol-version=0 server=node software-version=0.2.1
5:03PM INF ABCI Replay Blocks appHeight=47168 module=consensus server=node stateHeight=47168 storeHeight=47168
5:03PM INF Completed ABCI Handshake - CometBFT and App are synced appHash="�6;�?������\x0f4�G$f}u����\x06h��ڀ\x17��" appHeight=47168 module=consensus server=node
5:03PM INF Version info abci=0.17.0 block=11 cmtbft_version=0.34.27 commit_hash= p2p=8 server=node
5:03PM INF This node is a validator addr=AAE081879F012B73EDF6F6F2F0A95D705AF2F7A4 module=consensus pubKey=EAwuILEBxcmzXbak4k821JiPKLu9UIwbH6fPWDkkXRo= server=node
5:03PM INF P2P Node ID ID=946b549550e9c564193bf4c963d84b17e5415a50 file=/root/.humansd/config/node_key.json module=p2p server=node
5:03PM INF Adding persistent peers addrs=[] module=p2p server=node
5:03PM INF Adding unconditional peer ids ids=[] module=p2p server=node
5:03PM INF Add our address to book addr={"id":"946b549550e9c564193bf4c963d84b17e5415a50","ip":"0.0.0.0","port":26656} book=/root/.humansd/config/addrbook.json module=p2p server=node
5:03PM INF service start impl=Node msg={} server=node
5:03PM INF Starting pprof server laddr=localhost:6060 server=node
5:03PM INF service start impl="P2P Switch" module=p2p msg={} server=node
5:03PM INF serve module=rpc-server msg={} server=node
5:03PM INF service start impl=ConsensusReactor module=consensus msg={} server=node
5:03PM INF Reactor  module=consensus server=node waitSync=true
5:03PM INF service start impl=Evidence module=evidence msg={} server=node
5:03PM INF service start impl=StateSync module=statesync msg={} server=node
5:03PM INF service start impl=PEX module=pex msg={} server=node
5:03PM INF service start book=/root/.humansd/config/addrbook.json impl=AddrBook module=p2p msg={} server=node
5:03PM INF service start impl=BlockchainReactor module=blockchain msg={} server=node
5:03PM INF service start impl=BlockPool module=blockchain msg={} server=node
5:03PM INF Ensure peers module=pex numDialing=0 numInPeers=0 numOutPeers=0 numToDial=10 server=node
5:03PM INF Saving AddrBook to file book=/root/.humansd/config/addrbook.json module=p2p server=node size=120
5:03PM INF service start impl="Peer{MConn{94.237.27.19:26656} 42f95015c31c7814b6a0a717fd8c63d15f896e88 out}" module=p2p msg={} peer={"id":"42f95015c31c7814b6a0a717fd8c63d15f896e88","ip":"94.237.27.19","port":26656} server=node
5:03PM INF service start impl=MConn{94.237.27.19:26656} module=p2p msg={} peer={"id":"42f95015c31c7814b6a0a717fd8c63d15f896e88","ip":"94.237.27.19","port":26656} server=node
5:03PM INF service start impl="Peer{MConn{141.95.99.214:14356} 49f2ffa9786690548a1094b620d869ed72a33f8c out}" module=p2p msg={} peer={"id":"49f2ffa9786690548a1094b620d869ed72a33f8c","ip":"141.95.99.214","port":14356} server=node
5:03PM INF service start impl=MConn{141.95.99.214:14356} module=p2p msg={} peer={"id":"49f2ffa9786690548a1094b620d869ed72a33f8c","ip":"141.95.99.214","port":14356} server=node
5:03PM INF service start impl=EVMIndexerService indexer=evm msg={}
5:03PM INF service start impl="Peer{MConn{38.146.3.209:26656} 6c2581bce457207a8d29895216a06f0f98d39599 out}" module=p2p msg={} peer={"id":"6c2581bce457207a8d29895216a06f0f98d39599","ip":"38.146.3.209","port":26656} server=node
5:03PM INF service start impl=MConn{38.146.3.209:26656} module=p2p msg={} peer={"id":"6c2581bce457207a8d29895216a06f0f98d39599","ip":"38.146.3.209","port":26656} server=node
5:03PM INF Time to switch to consensus reactor! height=47169 module=blockchain server=node
5:03PM INF service stop impl={"Logger":{}} module=blockchain msg={} server=node
5:03PM INF SwitchToConsensus module=consensus server=node
5:03PM INF service start impl=ConsensusState module=consensus msg={} server=node
5:03PM INF service start impl=baseWAL module=consensus msg={} server=node wal=/root/.humansd/data/cs.wal/wal
5:03PM INF service start impl=Group module=consensus msg={} server=node wal=/root/.humansd/data/cs.wal/wal
5:03PM INF service start impl=TimeoutTicker module=consensus msg={} server=node
5:03PM INF Searching for height height=47169 max=4078 min=3980 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
5:03PM INF Searching for height height=47168 max=4078 min=3980 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
5:03PM INF Found height=47168 index=4078 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
5:03PM INF Catchup by replaying consensus messages height=47169 module=consensus server=node
5:03PM INF Replay: New Step height=47169 module=consensus round=0 server=node step=RoundStepNewHeight
```

