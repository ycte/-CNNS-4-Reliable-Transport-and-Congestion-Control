## two-node fishnet emulation 的输出和相应的标记

###### 22920202202763

1. make 的截图来证明编译过程

![image-20221130173124005](/home/yunxi/.config/Typora/typora-user-images/image-20221130173124005.png)



2. 启动两个节点

```shell
yunxi@yunxi-virtual-machine:~/Desktop/CNNS/assign4/forIDEA/fishnetJava-1.8-link$ perl trawler.pl 8888 scripts/two.topo
Trawler awaiting fish...
Got port 10000: assigning addr: 0
Got port 10001: assigning addr: 1
```

```shell
yunxi@yunxi-virtual-machine:~/Desktop/CNNS/assign4/forIDEA/fishnetJava-1.8-link$ perl fishnet.pl emulate localhost 8888 10000
Node 0: started
server 21 2
Node 1: server started, port = 21

```

```shell
yunxi@yunxi-virtual-machine:~/Desktop/CNNS/assign4/forIDEA/fishnetJava-1.8-link$ perl fishnet.pl emulate localhost 8888 10001
Node 1: started

```



3. node 1 向 node 0 传输 50000 bit 的包

* 配置文件 two.topo

```
edge 0 1 lossRate 0.05 delay 5 bw 10000 bt 1000
```

* node 0 (server)

```shell
yunxi@yunxi-virtual-machine:~/Desktop/CNNS/assign4/forIDEA/fishnetJava-1.8-link$ perl fishnet.pl emulate localhost 8888 10000
Node 0: started
server 21 2
Node 0: server started, port = 21
SNode 0: time = 1670150125131 msec
Node 0: connection accepted
............................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................FNode 0: time = 1670150136145 msec
Node 0: connection closed
Node 0: total bytes received = 50000

```

* node 1 (transfer client)

```shell
yunxi@yunxi-virtual-machine:~/Desktop/CNNS/assign4/forIDEA/fishnetJava-1.8-link$ perl fishnet.pl emulate localhost 8888 10001
Node 1: started
transfer 0 21 40 50000
S:Node 1: time = 1670150125940 msec
Node 1: started
Node 1: bytes to send = 50000
.:..:..:..:..:..:..:..:..:..:..:..:....:..:..:..:..:....:..:..:..:..:..:..:..:..:..:..:..???................???????????????????????:.:.:.:.:.:.:.:.:.:.:.:.:.:.???........????????????:.:.:..:.:.:.:.:.:.:.:.:..:.:.:.:.:.:.:.:.:.:..:.:.:.:..:.:.:.:.:.:.:..:.:.:.:.:.:.???......?????:.:.:..:..:.:.:.:.:.:..:.:.???....????:.???..:..:.:..:..:.:.:.:..:.:.:.:.:..:.:.:..:.:.:..:.:.:.???....???:.:.:.:..???..?:..:.:.:..:.:.:.:..???..:..:.:.:..:.:.:.:..:..:.:.:..:.:.:.:..:.:..:.:.:.:.:.:..:..:.:.:.:.:.:.:.:..:.:.:.:.:.:.:.:.:..:.:.:.:.:.:.:.:.:.:..:.:.:..:.:.:.:.:.:.:.:..:.:.:.:.:.:.???......????????:.:.:..:.:.:.:.:.:.:..:.:.:.:.:.:.:.:..:.:.:.:.:.:.:..:.:....:.:.:.:.:.:.:.:.:.:..:.:.:.???.....???????:..:.:..:.:.:.:..:.:.:.:.:.:.:..:.:.:..???....?:.??!.:..:..:.:..:..:.:.:..:.:.:.:.:..:.:..:.:.:.:..:.:.:.:.???....???:.:.:..???..:..:.:.:..:.:.:.:..:.:.:.:.:..:.:.???...??:.:..:.:.??!.:..:....?!.?:..:..:.:.:..:.:.:.:..:.:.???..?:..:.:..:.:..???..:..:..:.:..:.:.:.:..:.:.:.:.:..???...??:.:.:..:.:.:..:.:.:.???...?:.:.:..:.:.:.:..:.:.:.:.:..:.:.:.:.:.:..:.:.:.:.:.:.:..:.:.:.:.???....????:.:..:.:.:.:.:..:.:.:.:.:.:..:.:.:.:.:.:.:..:.:.:.:.:.:.:.:..Node 1: time = 1670150133952
Node 1: sending completed
Node 1: closing connection...
???....??:..:.:.:.:.:..:.:.:.:.:.:..:.:.:.:.:.:.:...:.:.:.:.:.:.:.:..:.:.:.:.:.:.???.....???:.:.:.:.:..???...??:.:.:..:.:.:..::::!.:Node 1: time = 1670150135953 msec
Node 1: connection closed
Node 1: total bytes sent = 50000
Node 1: time elapsed = 10013 msec
Node 1: Bps = 4993.508439029262

```



4. What is going on ?

* client 向 server 发送 SYN 请求，server 收到 SYN 数据包 (图中的 ‘S：Node 1’, 'SNode 0')

![image-20221204184624380](/home/yunxi/.config/Typora/typora-user-images/image-20221204184624380.png)

![image-20221204184733167](/home/yunxi/.config/Typora/typora-user-images/image-20221204184733167.png)

* server 向 client 回复 —— 发送 SYN （S） 和 ACK （:），图同上
* client 收到 ACK 后开始向 server 发送 packet，共计 50000 字节，中间 server 每收到一个 DATA packet 都会在屏幕上打印一个 ‘.’，并检查包的大小是否一致，将每个包的 ACK 信息发送回 client。如下图 (图中 ‘.’ 表示发送一个 DATA packet，‘:’ 表示收到一个推进字段的 ACK，‘.’ 表示收到一个不推进字段的 ACK， ‘!’ 表示 client 向 server 重传一个 DATA packet）：

![image-20221204185824638](/home/yunxi/.config/Typora/typora-user-images/image-20221204185824638.png)

* 全部包发送完成时，client 向 server 发送一个 FIN 包，如下图 server 收到的 ‘F’：

![image-20221204190048940](/home/yunxi/.config/Typora/typora-user-images/image-20221204190048940.png)
