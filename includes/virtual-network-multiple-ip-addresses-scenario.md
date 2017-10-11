## <a name="scenario"></a>方案
创建了一个 NIC 的虚拟机并将其连接到虚拟网络。 VM 需要三个不同*私有*IP 地址和第二个*公共*IP 地址。 IP 地址分配给以下 IP 配置：

* **IPConfig-1:**分配*静态*专用 IP 地址和一个*静态*公共 IP 地址。
* **IPConfig-2:**分配*静态*专用 IP 地址和一个*静态*公共 IP 地址。
* **IPConfig-3:**分配*静态*专用 IP 地址和没有公共 IP 地址。
  
    ![多个 IP 地址](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

当创建 NIC 并 NIC 连接到 VM 时创建的 VM，IP 配置将关联到 NIC。 为方案使用的 IP 地址的类型是为了进行说明。 你可以分配任何需要的 IP 地址和分配类型。

> [!NOTE]
> 尽管这篇文章中的步骤将 IP 配置都分配给了一个 NIC，你还可以为多 NIC VM 中的任何 NIC 分配多个 IP 配置。 若要了解如何创建具有多个 Nic 的 VM，读取[创建具有多个 Nic 的 VM](../articles/virtual-network/virtual-network-deploy-multinic-arm-ps.md)文章。