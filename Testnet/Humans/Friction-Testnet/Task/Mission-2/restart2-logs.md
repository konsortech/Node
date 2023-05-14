## Restart 2 - Sun May 14 2023 5:43PM UTC

```
service humansd restart && journalctl -fu humansd -o cat

5:43PM ERR Error stopping pool err="already stopped" module=blockchain server=node
5:43PM INF Saving AddrBook to file book=/root/.humansd/config/addrbook.json module=p2p server=node size=119
5:43PM INF Closing rpc listener listener={"Listener":{}} server=node
5:43PM ERR Stopped accept routine, as transport is closed module=p2p numPeers=0 server=node
5:43PM INF RPC HTTP server stopped err="accept tcp [::]:26657: use of closed network connection" module=rpc-server server=node
5:43PM ERR Error serving server err="accept tcp [::]:26657: use of closed network connection" server=node
humansd.service: Deactivated successfully.
Stopped humans.
humansd.service: Consumed 5min 42.424s CPU time.
Started humans.
5:43PM INF Unlocking keyring
5:43PM INF starting ABCI with Tendermint
5:43PM INF starting node with ABCI Tendermint in-process
5:43PM INF service start impl=multiAppConn module=proxy msg={} server=node
5:43PM INF service start connection=query impl=localClient module=abci-client msg={} server=node
5:43PM INF service start connection=snapshot impl=localClient module=abci-client msg={} server=node
5:43PM INF service start connection=mempool impl=localClient module=abci-client msg={} server=node
5:43PM INF service start connection=consensus impl=localClient module=abci-client msg={} server=node
5:43PM INF service start impl=EventBus module=events msg={} server=node
5:43PM INF service start impl=PubSub module=pubsub msg={} server=node
5:43PM INF service start impl=IndexerService module=txindex msg={} server=node
5:43PM INF ABCI Handshake App Info hash="��ȅ�y��Zp�\x1fRF������|����\x02 ��\x15\u008d" height=47568 module=consensus protocol-version=0 server=node software-version=0.2.1
5:43PM INF ABCI Replay Blocks appHeight=47568 module=consensus server=node stateHeight=47568 storeHeight=47568
5:43PM INF Completed ABCI Handshake - CometBFT and App are synced appHash="��ȅ�y��Zp�\x1fRF������|����\x02 ��\x15\u008d" appHeight=47568 module=consensus server=node
5:43PM INF Version info abci=0.17.0 block=11 cmtbft_version=0.34.27 commit_hash= p2p=8 server=node
5:43PM INF This node is a validator addr=AAE081879F012B73EDF6F6F2F0A95D705AF2F7A4 module=consensus pubKey=EAwuILEBxcmzXbak4k821JiPKLu9UIwbH6fPWDkkXRo= server=node
5:43PM INF P2P Node ID ID=946b549550e9c564193bf4c963d84b17e5415a50 file=/root/.humansd/config/node_key.json module=p2p server=node
5:43PM INF Adding persistent peers addrs=[] module=p2p server=node
5:43PM INF Adding unconditional peer ids ids=[] module=p2p server=node
5:43PM INF Add our address to book addr={"id":"946b549550e9c564193bf4c963d84b17e5415a50","ip":"0.0.0.0","port":26656} book=/root/.humansd/config/addrbook.json module=p2p server=node
5:43PM INF service start impl=Node msg={} server=node
5:43PM INF Starting pprof server laddr=localhost:6060 server=node
5:43PM INF service start impl="P2P Switch" module=p2p msg={} server=node
5:43PM INF service start impl=BlockchainReactor module=blockchain msg={} server=node
5:43PM INF service start impl=BlockPool module=blockchain msg={} server=node
5:43PM INF service start impl=ConsensusReactor module=consensus msg={} server=node
5:43PM INF Reactor  module=consensus server=node waitSync=true
5:43PM INF serve module=rpc-server msg={} server=node
5:43PM INF service start impl=Evidence module=evidence msg={} server=node
5:43PM INF service start impl=StateSync module=statesync msg={} server=node
5:43PM INF service start impl=PEX module=pex msg={} server=node
5:43PM INF service start book=/root/.humansd/config/addrbook.json impl=AddrBook module=p2p msg={} server=node
5:43PM INF Saving AddrBook to file book=/root/.humansd/config/addrbook.json module=p2p server=node size=119
5:43PM INF Ensure peers module=pex numDialing=0 numInPeers=0 numOutPeers=0 numToDial=10 server=node
5:43PM INF service start impl="Peer{MConn{138.201.121.185:26656} 6271d80b8fc42da3a2825cc5ef75818dd52423d1 out}" module=p2p msg={} peer={"id":"6271d80b8fc42da3a2825cc5ef75818dd52423d1","ip":"138.201.121.185","port":26656} server=node
5:43PM INF service start impl=MConn{138.201.121.185:26656} module=p2p msg={} peer={"id":"6271d80b8fc42da3a2825cc5ef75818dd52423d1","ip":"138.201.121.185","port":26656} server=node
5:43PM INF service start impl="Peer{MConn{141.95.34.193:46656} 417089d6681abacc685c2eff9e029d85231a04a0 out}" module=p2p msg={} peer={"id":"417089d6681abacc685c2eff9e029d85231a04a0","ip":"141.95.34.193","port":46656} server=node
5:43PM INF service start impl=MConn{141.95.34.193:46656} module=p2p msg={} peer={"id":"417089d6681abacc685c2eff9e029d85231a04a0","ip":"141.95.34.193","port":46656} server=node
5:43PM INF service start impl=EVMIndexerService indexer=evm msg={}
5:43PM INF service start impl="Peer{MConn{65.108.72.253:26656} bf99f84b1674f87ffe95972f332cb218d1253b9c out}" module=p2p msg={} peer={"id":"bf99f84b1674f87ffe95972f332cb218d1253b9c","ip":"65.108.72.253","port":26656} server=node
5:43PM INF service start impl=MConn{65.108.72.253:26656} module=p2p msg={} peer={"id":"bf99f84b1674f87ffe95972f332cb218d1253b9c","ip":"65.108.72.253","port":26656} server=node
5:43PM INF service start impl="Peer{MConn{135.181.142.117:26656} 459bcaea161d20cddcdead811d282bd495446cbb out}" module=p2p msg={} peer={"id":"459bcaea161d20cddcdead811d282bd495446cbb","ip":"135.181.142.117","port":26656} server=node
5:43PM INF service start impl=MConn{135.181.142.117:26656} module=p2p msg={} peer={"id":"459bcaea161d20cddcdead811d282bd495446cbb","ip":"135.181.142.117","port":26656} server=node
5:43PM INF service start impl="Peer{MConn{95.217.200.36:26656} 2f6cc8b0b255745d71c358351ddde1faa350b0be out}" module=p2p msg={} peer={"id":"2f6cc8b0b255745d71c358351ddde1faa350b0be","ip":"95.217.200.36","port":26656} server=node
5:43PM INF service start impl=MConn{95.217.200.36:26656} module=p2p msg={} peer={"id":"2f6cc8b0b255745d71c358351ddde1faa350b0be","ip":"95.217.200.36","port":26656} server=node
5:43PM INF service start impl="Peer{MConn{85.239.240.45:26656} 62fff57f15d7ba6fb8aecb9ae93ea58dd3cefea8 out}" module=p2p msg={} peer={"id":"62fff57f15d7ba6fb8aecb9ae93ea58dd3cefea8","ip":"85.239.240.45","port":26656} server=node
5:43PM INF service start impl=MConn{85.239.240.45:26656} module=p2p msg={} peer={"id":"62fff57f15d7ba6fb8aecb9ae93ea58dd3cefea8","ip":"85.239.240.45","port":26656} server=node
5:43PM INF service start impl="Peer{MConn{103.180.28.79:26656} 36f956fa2fe317a5d3163d0b6c7b104e33aa62e9 out}" module=p2p msg={} peer={"id":"36f956fa2fe317a5d3163d0b6c7b104e33aa62e9","ip":"103.180.28.79","port":26656} server=node
5:43PM INF service start impl=MConn{103.180.28.79:26656} module=p2p msg={} peer={"id":"36f956fa2fe317a5d3163d0b6c7b104e33aa62e9","ip":"103.180.28.79","port":26656} server=node
5:43PM INF Time to switch to consensus reactor! height=47569 module=blockchain server=node
5:43PM INF service stop impl={"Logger":{}} module=blockchain msg={} server=node
5:43PM INF SwitchToConsensus module=consensus server=node
5:43PM INF service start impl=ConsensusState module=consensus msg={} server=node
5:43PM INF service start impl=baseWAL module=consensus msg={} server=node wal=/root/.humansd/data/cs.wal/wal
5:43PM INF service start impl=Group module=consensus msg={} server=node wal=/root/.humansd/data/cs.wal/wal
5:43PM INF service start impl=TimeoutTicker module=consensus msg={} server=node
5:43PM INF Searching for height height=47569 max=4101 min=4003 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
5:43PM INF Searching for height height=47568 max=4101 min=4003 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
5:43PM INF Found height=47568 index=4101 module=consensus server=node wal=/root/.humansd/data/cs.wal/wal
5:43PM INF Catchup by replaying consensus messages height=47569 module=consensus server=node
5:43PM INF Replay: New Step height=47569 module=consensus round=0 server=node step=RoundStepNewHeight
```
