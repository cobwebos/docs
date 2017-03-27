
## <a name="key-features"></a>主要功能
* **高性能硬件** - 这些实例专为计算密集型和网络密集型应用程序而设计和进行优化，包括高性能计算 (HPC) 和批处理应用程序、建模及大规模仿真。 
  
    可在 Intel.com 网站上查看有关 Intel Xeon E5-2667 v3 处理器（用于 H 系列）和 Intel Xeon E5-2670 处理器（用于 A8 - A11）的详细信息，包括支持的指令集扩展。 
* **专为 HPC 群集设计** - 在 Azure 中部署多个计算密集型实例，创建独立 HPC 群集或为本地群集增添容量。 如果愿意，可部署群集管理和作业计划工具。 或者在其他 Azure 服务（如 Azure Batch）中使用计算密集型工作的实例。
* **MPI 应用程序的 RDMA 网络连接** - 计算密集型实例（H16r、H16mr、A8 和 A9）的子集具有远程直接内存访问 (RDMA) 连接的第二个网络接口。 此接口是对可供其他 VM 大小使用的标准 Azure 网络接口的补充。 
  
    此接口允许支持 RDMA 的实例在 InfiniBand 网络上相互通信，对 H16r 和 H16mr 虚拟机以 FDR 速率运行，对 A8 和 A9 虚拟机以 QDR 速率运行。 这些虚拟机中公开的 RDMA 功能可提高某些 Linux 和 Windows 消息传递接口 (MPI) 应用程序的可伸缩性和性能。 有关要求，请参阅本文中的 [RDMA 网络访问权限](#access-to-the-rdma-network)。

## <a name="deployment-considerations"></a>部署注意事项
* **Azure 订阅** - 若要部署不止一些计算密集型实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。
* **定价和可用性** - 只在标准定价层提供计算密集型 VM 大小。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/regions/services/)（按区域提供的产品）。 
* **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 H 系列）中部署的核心数目。 若要请求提高配额，可免费[提出在线客户支持请求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 （默认限制可能因你订阅的类别。）
  
  > [!NOTE]
  > 如果你有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  > 
  > 
* **虚拟网络** – Azure [虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)不需要使用计算密集型实例。 但是，如果需要访问本地资源（例如，应用程序许可证服务器），则对于许多部署方案或者一个站点到站点连接，可能至少需要一个基于云的 Azure 虚拟网络。 如果需要，请创建一个新的虚拟网络来部署实例。 不支持将计算密集型 VM 添加到地缘组中的虚拟网络。
* **云服务或可用性集** – 若要使用 Azure RDMA 网络，可在同一个云服务（如果使用的是经典部署模型）或同一个可用性集（如果使用的是 Azure Resource Manager 部署模型）中部署支持 RDMA 的 VM。 如果使用 Azure Batch，则支持 RDMA 的VM 必须位于同一池中。
* **调整大小** - 由于在计算密集型实例中使用了专用硬件，所以可以只对同一大小系列（H 系列或计算密集型 A 系列）内的计算密集型实例进行大小调整。 例如，可仅将 H 系列 VM 的大小从一个 H 系列大小调整为另一个。 此外，不支持从非计算密集型大小调整为计算密集型大小。  
* **RDMA 网络地址空间** - Azure 中的 RDMA 网络保留地址空间 172.16.0.0/16。 若要在 Azure 虚拟网络中部署的实例上运行 MPI 应用程序，请确保虚拟网络地址空间不与 RDMA 网络重叠。

