## <a name="tcp-settings-for-azure-vms"></a>Azure VM 的 TCP 设置
Azure VM 使用 [NAT][nat]（网络地址转换）来与公共 Internet 通信。 NAT 设备将公共 IP 地址和端口分配给 Azure VM，使该 VM 能够与其他设备建立通信套接字。 如果数据包在特定时间后不再流经该套接字，NAT 设备将终止映射，因此该套接字可供其他 VM 使用。

这是一种常见的 NAT 行为，但对于希望即使超过超时期限也仍要保留套接字的基于 TCP 的应用程序而言，这种行为可能会造成通信问题。 对于处于*已建立连接*状态的会话，可以考虑使用以下两种空闲超时设置：

* 通过 [Azure Load Balancer][azure-lb-timeout] **入站**。 此超时默认为 4 分钟，最大可调整为 30 分钟。
* 使用 [SNAT][snat]（源 NAT）**出站**。 此超时设置为 4 分钟，无法调整。

为了确保不在超过超时限制时断开连接，应用程序必须将会话保持活动状态；或者，你可以将基础操作系统配置为将会话保持活动状态。 在 Linux 和 Windows 系统中使用的设置不同，如下所述。

对于 [Linux][linux]，应更改以下内核变量。
net.ipv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8

对于 [Windows][windows]，应更改以下注册表值。
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8

使用上述设置可确保在空闲 2 分钟（120 秒）后发送 keep-alive 数据包，然后每隔 30 秒发送一次。 如果发送这些数据包失败 8 次，将丢弃会话。

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md

<!--HONumber=Jan17_HO3-->


