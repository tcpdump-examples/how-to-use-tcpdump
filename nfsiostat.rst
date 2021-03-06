https://www.howtouselinux.com/post/use-linux-nfsiostat-to-troubleshoot-nfs-performance-issue

Understanding nfsiostat command output

The following is the output of nfsiostat.  We can get the NFS performance metrics here like NFS IOPS, bandwidth, latency.

op/s
This is the number of operations per second.

rpc bklog
This is the length of the backlog queue.

kB/s
This is the number of kB written/read per second.

kB/op
This is the number of kB written/read per each operation.

retrans
This is the number of retransmissions.

avg RTT (ms)
This is the duration from the time that client's kernel sends the RPC request until the time it receives the reply.

avg exe (ms)
This is the duration from the time that NFS client does the RPC request to its kernel until the RPC request is completed, this includes the RTT time above.

