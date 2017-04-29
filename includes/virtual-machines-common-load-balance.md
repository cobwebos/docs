

Azure 基础结构服务提供两个级别的负载平衡：

* **DNS 级别**：使到以下位置的流量实现负载平衡：位于不同数据中心的不同云服务、位于不同数据中心的不同 Azure 网站，或者外部终结点。 可以通过 Azure 流量管理器和轮循机制负载平衡方法实现此目的。
* **网络级别**：使传入云服务的不同虚拟机中的 Internet 流量实现负载平衡，或者使云服务或虚拟网络中虚拟机间的流量实现负载平衡。 这是通过 Azure 负载均衡器完成的。

## <a name="traffic-manager-load-balancing-for-cloud-services-and-websites"></a>使用流量管理器针对云服务和网站实现负载平衡
使用流量管理器可控制用户流量到终结点（可包括云服务、网站、外部站点和其他流量管理器配置文件）的分发过程。 流量管理器的工作原理是将智能策略引擎应用到 Internet 资源域名的域名系统 (DNS) 查询。 云服务或网站可以在世界各地的不同数据中心中运行。

必须使用 REST 或 Windows PowerShell 将外部终结点或流量管理器配置文件配置为终结点。

流量管理器使用三种负载平衡方法分发流量：

* **故障转移**：请在以下情况下使用此方法：希望对所有流量使用主终结点，但希望在主终结点不可用时提供备份。
* **性能**：请在以下情况下使用此方法：在不同地理位置拥有多个终结点，并且希望请求客户端使用“最靠近的”终结点（因为延迟最低）。
* **轮循机制：**请在以下情况下使用此方法：要将负载分发到位于同一数据中心的一组云服务，或位于不同数据中心的多个云服务或网站。

有关详细信息，请参阅[关于流量管理器负载平衡方法](../articles/traffic-manager/traffic-manager-routing-methods.md)。

下图显示了用于将流量分发到不同云服务的轮循机制负载平衡方法示例。

![loadbalancing](./media/virtual-machines-common-load-balance/TMSummary.png)

基本过程如下：

1. Internet 客户端查询与 Web 服务相对应的域名。
2. DNS 将该名称查询请求转发到流量管理器。
3. 流量管理器在轮循机制列表中选择下一个云服务并发回 DNS 名称。 Internet 客户端的 DNS 服务器将该名称解析为 IP 地址，然后将其发送到 Internet 客户端。
4. Internet 客户端通过流量管理器与选择的云服务建立连接。

有关详细信息，请参阅[流量管理器](../articles/traffic-manager/traffic-manager-overview.md)。

## <a name="azure-load-balancing-for-virtual-machines"></a>针对虚拟机的 Azure 负载平衡
对于同一云服务或虚拟网络中的虚拟机，可使用其专用 IP 地址直接相互通信。 对于该云服务或虚拟网络以外的计算机和服务，仅可通过配置的终结点与某个云服务或虚拟网络中的虚拟机通信。 终结点是公用 IP 地址和端口到 Azure 云服务中虚拟机或 Web 角色的专用 IP 地址和端口的映射。

在名为“负载平衡集”的配置中，Azure Load Balancer 将特定类型的传入流量随机分发到多个虚拟机或服务。 例如，可将 Web 请求流量负载分配到多个 Web 服务器或 Web 角色。

下图显示由三台虚拟机共享的公用和专用 TCP 端口 80 的标准（未加密）Web 流量负载平衡终结点。 三台虚拟机位于负载平衡集中。

![loadbalancing](./media/virtual-machines-common-load-balance/LoadBalancing.png)

有关详细信息，请参阅 [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md)。 有关创建负载平衡集的步骤，请参阅[配置负载平衡集](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md)。

Azure 还可以在云服务或虚拟网络中实现负载平衡。 这称为“内部负载平衡”，可以通过以下方式使用：

* 在多层应用程序的不同层（如在 Web 层和数据库层之间）中的服务器之间实现负载平衡。
* 使托管在 Azure 中的业务线 (LOB) 应用程序实现负载平衡，而无需额外的负载平衡器硬件或软件。
* 在实现流量负载平衡的一组计算机集中包括本地服务器。

与 Azure 负载平衡类似，可通过配置内部负载平衡集实现内部负载平衡。

下图显示在跨虚拟网络的三台虚拟机间共享业务线 (LOB) 应用程序的内部负载平衡终结点示例。

![loadbalancing](./media/virtual-machines-common-load-balance/LOBServers.png)

## <a name="load-balancer-considerations"></a>负载均衡器注意事项
默认情况下，负载均衡器配置为会话空闲 4 分钟即超时。 如果位于负载均衡器后面的应用程序的连接空闲时间超过 4 分钟且没有“保持活动状态”配置，则该连接会断开。 可以更改负载均衡器的行为，[延长 Azure Load Balancer 的超时设置](../articles/load-balancer/load-balancer-tcp-idle-timeout.md)。

另一考虑因素是 Azure Load Balancer 支持的分发模式类型。 可以配置源 IP 关联（源 IP、目标 IP）或源 IP 协议（源 IP、目标 IP 和协议）。 有关详细信息，请参阅 [Azure Load Balancer 分发模式（源 IP 关联）](../articles/load-balancer/load-balancer-distribution-mode.md)。

## <a name="next-steps"></a>后续步骤
有关创建负载平衡集的步骤，请参阅[配置内部负载平衡集](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md)。

有关负载均衡器的详细信息，请参阅[内部负载平衡](../articles/load-balancer/load-balancer-internal-overview.md)。

