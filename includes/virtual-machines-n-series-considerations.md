## <a name="deployment-considerations"></a>部署注意事项

* 有关 N 系列 VM 的可用性，请查看[可用产品(按区域)](https://azure.microsoft.com/en-us/regions/services/)。

* N 系列 VM 只能按 Resource Manager 部署模型部署。

* N 系列的 VM 在对其磁盘支持的 Azure 存储类型方面有所不同。 NC 和 NV VM 仅支持标准磁盘存储 (HDD) 所支持的 VM 磁盘。 NCv2、ND 和 NCv3（预览版）VM 仅支持高级磁盘存储 (SSD) 所支持的 VM 磁盘。

* 如果需要部署的 N 系列 VM 较多，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。

* 可能需要提高 Azure 订阅中的核心配额（按区域）以及单独针对 NC、NCv2、ND 或 NV 核心的配额。 若要请求提高配额，可免费[提出在线客户支持请求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 默认限制可能因订阅类别而异。

* 可以部署在 N 系列 VM 上的一个 VM 映像是 [Azure 数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)。 数据科学虚拟机预安装并配置了许多热门的数据科学工具和深度学习工具。 它还预安装了 NVIDIA Tesla GPU 驱动程序。





