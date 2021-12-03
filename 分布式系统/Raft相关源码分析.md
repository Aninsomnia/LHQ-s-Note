# 节点初始化

### raft\node.go

```go
// StartNode returns a new Node given configuration and a list of raft peers.
// It appends a ConfChangeAddNode entry for each given peer to the initial log.
//
// Peers must not be zero length; call RestartNode in that case.
// StartNode将返回一个根据给定配置和peers进行初始化的新节点
// 并且会为每一个给定的peer追加一个ConfChangeAddNode日志条目
func StartNode(c *Config, peers []Peer) Node {
	if len(peers) == 0 {
		panic("no peers given; use RestartNode instead")
	}
	rn, err := NewRawNode(c)
	if err != nil {
		panic(err)
	}
	err = rn.Bootstrap(peers)
	if err != nil {
		c.Logger.Warningf("error occurred during starting a new node: %v", err)
	}

	n := newNode(rn)

	go n.run()
	return &n
}
```

