Azure 负载平衡器是层-4 （TCP、 UDP） 负载平衡器。 负载平衡器通过将传入通信中云服务的运行状况服务实例，或者在负载平衡器集的虚拟机分布提供高可用性。 Azure 负载平衡器可以显示在多个端口和 / 或多个 IP 地址，这些服务。

你可以配置负载平衡器用于：

* 负载平衡传入 Internet 流量的虚拟机 (Vm)。 我们将在此方案中作为负载平衡器[面向 Internet 的负载平衡器](../articles/load-balancer/load-balancer-internet-overview.md)。
* 虚拟网络 (VNet) 中的 Vm 之间、 云服务中的 Vm 之间或在本地计算机和跨界虚拟网络中的 Vm 之间的负载平衡流量。 我们将在此方案中作为负载平衡器[内部负载平衡器 (ILB)](../articles/load-balancer/load-balancer-internal-overview.md)。
* 外部将流量转发到特定的 VM 实例。
