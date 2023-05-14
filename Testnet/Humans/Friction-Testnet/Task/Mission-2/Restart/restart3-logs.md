## Restart 3 - 

```
service humansd restart && journalctl -fu humansd -o cat

6:52PM INF service stop book=/root/.humansd/config/addrbook.json impl={"Logger":{}} module=p2p msg={} server=node
6:52PM INF Saving AddrBook to file book=/root/.humansd/config/addrbook.json module=p2p server=node size=120
6:52PM ERR Stopped accept routine, as transport is closed module=p2p numPeers=0 server=node
6:52PM INF Closing rpc listener listener={"Listener":{}} server=node
6:52PM INF RPC HTTP server stopped err="accept tcp [::]:26657: use of closed network connection" module=rpc-server server=node
6:52PM ERR Error serving server err="accept tcp [::]:26657: use of closed network connection" server=node
humansd.service: Deactivated successfully.
Stopped humans.
humansd.service: Consumed 9min 28.690s CPU time.
Started humans.
6:52PM INF Unlocking keyring
6:52PM INF starting ABCI with Tendermint
6:52PM INF starting node with ABCI Tendermint in-process
6:52PM INF service start impl=multiAppConn module=proxy msg={} server=node
6:52PM INF service start connection=query impl=localClient module=abci-client msg={} server=node
6:52PM INF service start connection=snapshot impl=localClient module=abci-client msg={} server=node
6:52PM INF service start connection=mempool impl=localClient module=abci-client msg={} server=node
6:52PM INF service start connection=consensus impl=localClient module=abci-client msg={} server=node
6:52PM INF service start impl=EventBus module=events msg={} server=node
6:52PM INF service start impl=PubSub module=pubsub msg={} server=node
6:52PM INF service start impl=IndexerService module=txindex msg={} server=node
6:52PM INF ABCI Handshake App Info hash="�ޔ�\x14�\r6M���vѩ�L_J�\x13d\t��U$;�\an\x06" height=48242 module=consensus protocol-version=0 server=node software-version=0.2.1
6:52PM INF ABCI Replay Blocks appHeight=48242 module=consensus server=node stateHeight=48242 storeHeight=48242
6:52PM INF Completed ABCI Handshake - CometBFT and App are synced appHash="�ޔ�\x14�\r6M���vѩ�L_J�\x13d\t��U$;�\an\x06" appHeight=48242 module=consensus server=node
6:52PM INF Version info abci=0.17.0 block=11 cmtbft_version=0.34.27 commit_hash= p2p=8 server=node
6:52PM INF This node is a validator addr=AAE081879F012B73EDF6F6F2F0A95D705AF2F7A4 module=consensus pubKey=EAwuILEBxcmzXbak4k821JiPKLu9UIwbH6fPWDkkXRo= server=node
6:52PM INF P2P Node ID ID=946b549550e9c564193bf4c963d84b17e5415a50 file=/root/.humansd/config/node_key.json module=p2p server=node
6:52PM INF Adding persistent peers addrs=[] module=p2p server=node
6:52PM INF Adding unconditional peer ids ids=[] module=p2p server=node
6:52PM INF Add our address to book addr={"id":"946b549550e9c564193bf4c963d84b17e5415a50","ip":"0.0.0.0","port":26656} book=/root/.humansd/config/addrbook.json module=p2p server=node
6:52PM INF service start impl=Node msg={} server=node
6:52PM INF Starting pprof server laddr=localhost:6060 server=node
6:52PM INF serve module=rpc-server msg={} server=node
6:52PM INF service start impl="P2P Switch" module=p2p msg={} server=node
6:52PM INF service start impl=StateSync module=statesync msg={} server=node
6:52PM INF service start impl=PEX module=pex msg={} server=node
6:52PM INF service start book=/root/.humansd/config/addrbook.json impl=AddrBook module=p2p msg={} server=node
6:52PM INF service start impl=BlockchainReactor module=blockchain msg={} server=node
6:52PM INF service start impl=BlockPool module=blockchain msg={} server=node
6:52PM INF service start impl=ConsensusReactor module=consensus msg={} server=node
6:52PM INF Reactor  module=consensus server=node waitSync=true
6:52PM INF service start impl=Evidence module=evidence msg={} server=node
6:52PM INF Ensure peers module=pex numDialing=0 numInPeers=0 numOutPeers=0 numToDial=10 server=node
6:52PM INF Saving AddrBook to file book=/root/.humansd/config/addrbook.json module=p2p server=node size=120
6:52PM INF Inbound Peer rejected err="filtered CONN<136.243.136.241:41868>: duplicate CONN<136.243.136.241:41868>" module=p2p numPeers=0 server=node
6:52PM INF service start impl="Peer{MConn{195.201.59.194:26656} b1f13e9971cfdcf784fb0efbd1b72417d5410a02 out}" module=p2p msg={} peer={"id":"b1f13e9971cfdcf784fb0efbd1b72417d5410a02","ip":"195.201.59.194","port":26656} server=node
6:52PM INF service start impl=MConn{195.201.59.194:26656} module=p2p msg={} peer={"id":"b1f13e9971cfdcf784fb0efbd1b72417d5410a02","ip":"195.201.59.194","port":26656} server=node
6:52PM INF service start impl="Peer{MConn{141.95.99.214:14356} 49f2ffa9786690548a1094b620d869ed72a33f8c out}" module=p2p msg={} peer={"id":"49f2ffa9786690548a1094b620d869ed72a33f8c","ip":"141.95.99.214","port":14356} server=node
6:52PM INF service start impl=MConn{141.95.99.214:14356} module=p2p msg={} peer={"id":"49f2ffa9786690548a1094b620d869ed72a33f8c","ip":"141.95.99.214","port":14356} server=node
6:52PM INF service start impl="Peer{MConn{212.47.234.144:26656} 19230fad7145e6fe80566a72f66b9ca3ec3f04d5 out}" module=p2p msg={} peer={"id":"19230fad7145e6fe80566a72f66b9ca3ec3f04d5","ip":"212.47.234.144","port":26656} server=node
6:52PM INF service start impl=MConn{212.47.234.144:26656} module=p2p msg={} peer={"id":"19230fad7145e6fe80566a72f66b9ca3ec3f04d5","ip":"212.47.234.144","port":26656} server=node
6:52PM INF service start impl="Peer{MConn{65.109.92.235:26656} c47471d15b4cd8974624b01b71b3058c3ece4679 out}" module=p2p msg={} peer={"id":"c47471d15b4cd8974624b01b71b3058c3ece4679","ip":"65.109.92.235","port":26656} server=node
6:52PM INF service start impl=MConn{65.109.92.235:26656} module=p2p msg={} peer={"id":"c47471d15b4cd8974624b01b71b3058c3ece4679","ip":"65.109.92.235","port":26656} server=node
6:52PM INF service start impl=EVMIndexerService indexer=evm msg={}
6:52PM INF service start impl="Peer{MConn{38.146.3.209:26656} 6c2581bce457207a8d29895216a06f0f98d39599 out}" module=p2p msg={} peer={"id":"6c2581bce457207a8d29895216a06f0f98d39599","ip":"38.146.3.209","port":26656} server=node
6:52PM INF service start impl=MConn{38.146.3.209:26656} module=p2p msg={} peer={"id":"6c2581bce457207a8d29895216a06f0f98d39599","ip":"38.146.3.209","port":26656} server=node
6:52PM INF service start impl="Peer{MConn{103.180.28.79:26656} 36f956fa2fe317a5d3163d0b6c7b104e33aa62e9 out}" module=p2p msg={} peer={"id":"36f956fa2fe317a5d3163d0b6c7b104e33aa62e9","ip":"103.180.28.79","port":26656} server=node
6:52PM INF service start impl=MConn{103.180.28.79:26656} module=p2p msg={} peer={"id":"36f956fa2fe317a5d3163d0b6c7b104e33aa62e9","ip":"103.180.28.79","port":26656} server=node
6:52PM INF Time to switch to consensus reactor! height=48243 module=blockchain server=node
6:52PM INF service stop impl={"Logger":{}} module=blockchain msg={} server=node
6:52PM INF SwitchToConsensus module=consensus server=node
6:52PM INF service start impl=ConsensusState module=consensus msg={} server=node
6:52PM INF service start impl=baseWAL module=consensus msg={} server=node wal=/root/.humansd/data/cs.wal/wal
6:52PM INF service start impl=Group module=consensus msg={} server=node wal=/root/.humansd/data/cs.wal/wal
6:52PM INF service start impl=TimeoutTicker module=consensus msg={} server=node
6:52PM INF Searching for height height=48243 max=4144 min=4046 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
6:52PM INF Searching for height height=48242 max=4144 min=4046 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
6:52PM INF Found height=48242 index=4144 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
6:52PM INF Catchup by replaying consensus messages height=48243 module=consensus server=node
6:52PM INF Replay: New Step height=48243 module=consensus round=0 server=node step=RoundStepNewHeight
6:52PM INF Replay: Vote blockID={"hash":"AD00945F606766D5EB63A5C0F496B002DF4B92CEABD4CFAB6F42C4B5D8C8CEF2","parts":{"hash":"CCB2689C582C07AEA395809AF7A6A123FB4CB3CAEEA7DD966B5424A85918646A","total":1}} height=48242 module=consensus peer=ee8a0664518c5ef5078ad2251275d5689fcf96fb round=0 server=node type=1
```
