
## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘和以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
- 数据磁盘可以在缓存或非缓存模式下运行。 对于缓存数据磁盘操作，主机缓存模式设置为 **ReadOnly** 或 **ReadWrite**。  对于非缓存数据磁盘操作，主机缓存模式设置为 **None**。
- **预期的网络性能**是每个 VM 类型分配所有的 Nic，为所有目标之间的最大聚合的带宽。 上限没有保证，但目的是提供相关指导，以便为目标应用程序选择适当的 VM 类型。 实际的网络性能将取决于许多因素，包括网络拥塞、应用程序负载和网络设置。 有关如何优化网络吞吐量的信息，请参阅[为 Windows 和 Linux 优化网络吞吐量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 中达到预期的网络性能，可能需要选择特定的版本，或者需要优化 VM。 有关详细信息，请参阅[如何针对虚拟机吞吐量进行可靠的测试](../articles/virtual-network/virtual-network-bandwidth-testing.md)。

- &#8224;16 vCPU 性能一致地将到达即将发布的版本中的上限。


