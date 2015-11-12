<properties
   pageTitle="大型计算：用于批处理和高性能计算 (HPC) 的技术资源 | Microsoft Azure"
   description="列出了旨在帮助你在 Azure 中运行大规模并行、批处理和 HPC 工作负荷的技术资源。"
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.date="09/29/2015"
   wacn.date=""/>

# Azure 中的大型计算：用于批处理和高性能计算 (HPC) 的技术资源
这是一份技术资源指南，旨在帮助你在 Azure 中运行大规模并行、批处理和 HPC 工作负荷。在 Azure 中，可以使用各种 Azure 服务将现有的批处理或 HPC 工作负荷扩展到 Azure 云，或者生成新的大型计算解决方案。

## 解决方案选项

了解 Azure 中的大型计算选项，并根据工作负荷和业务需要选择适当的方法。

* [Batch 和 HPC 解决方案](batch-hpc-solutions.md)


## Azure 批处理

[Batch ](/documentation/services/batch/) 是一种平台服务，可让你轻松地在应用程序中启用云功能和运行作业，而无需设置和管理群集与作业计划程序。使用 SDK 可将不同语言的客户端应用与 Azure Batch 集成相集成，将数据迁移到 Azure，以及生成作业运行管道。

* [文档](/documentation/services/batch/)

* [API 参考](https://msdn.microsoft.com/zh-cn/library/azure/dn820177.aspx)

* [教程：适用于 .NET 的 Azure Batch 库入门](/documentation/articles/batch-dotnet-get-started)

* [Batch 论坛](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

## HPC 群集解决方案

将现有的 Windows 或 Linux HPC 群集部署或扩展到 Azure，以运行计算密集型工作负荷。

### Microsoft HPC Pack

HPC Pack 是在 Microsoft Azure 和 Windows Server 技术基础之上构建的 Microsoft 免费 HPC 解决方案。

* [下载 HPC Pack 2012 R2 Update 2](https://www.microsoft.com/zh-cn/download/details.aspx?id=47755)

* [文档](https://technet.microsoft.com/zh-cn/library/jj899572.aspx)


* [在 Azure 中使用 Microsoft HPC Pack 时的 HPC 群集选项](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [使用 HPC Pack 迸发到 Azure](https://technet.microsoft.com/library/gg481749.aspx)


* [Windows HPC 论坛](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux 和 OSS 群集解决方案

结合开放源代码工具使用这些 Azure 快速入门模板来部署 Linux HPC 群集。

* [运转 SLURM 群集](http://azure.microsoft.com/documentation/templates/slurm/)和[博客文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [运转 Torque 群集](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/zh-cn/library/bb524831.aspx) (MS-MPI) 是 Microsoft 实现的消息传递接口标准，用于在 Windows 平台上开发和运行并行应用程序。


* [下载 MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI 参考](https://msdn.microsoft.com/zh-cn/library/dn473458.aspx)

* [MPI 论坛](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## 计算密集型实例

Azure 提供[不同的大小](/documentation/articles/virtual-machines-size-specs)，包括计算密集型的 [A8、A9、A10 和 A11 实例](/documentation/articles/virtual-machines-a8-a9-a10-a11-specs)，用于运行 Linux 和 Windows HPC 工作负荷。

* [设置 Linux RDMA 群集以运行 MPI 应用程序](/documentation/articles/virtual-machines-linux-cluster-rdma)

* [通过 Microsoft HPC Pack 设置 Windows RDMA 群集以运行 MPI 应用程序](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## 体系结构蓝图

* [大规模计算 - 金融服务](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) 说明了如何使风险管理、报告和仿真在云具有可操作性，以及协调大规模计算与数据分析。

## 示例和演示

* [Azure Batch 代码示例](https://github.com/Azure/azure-batch-samples)

* [Batch Apps Blender 示例](https://github.com/Azure/azure-batch-apps-blender)和[博客文章](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## 相关的 Azure 服务

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory/)

* [机器学习](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [虚拟机](/documentation/services/virtual-machines/)

* [云服务](/documentation/services/cloud-services/)

* [媒体服务](/documentation/services/media-services/)



## 后续步骤

* 有关最新通告，请参阅 [Microsoft HPC 和批处理团队博客](http://blogs.technet.com/b/windowshpc/)与 [Azure 博客](http://azure.microsoft.com/blog/tag/hpc/)。
* 另请参阅 [Batch 中的新增功能](http://azure.microsoft.com/updates/?service=batch)或订阅 [RSS 源](http://azure.microsoft.com/updates/feed/?service=batch)。

<!---HONumber=79-->