---
title: 使用 Azure NetApp 文件的解决方案体系结构 |Microsoft Docs
description: 提供有关使用 Azure NetApp 文件的解决方案体系结构的最佳实践的参考。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: b-juche
ms.openlocfilehash: 0818d113b5475e847c009f979345e103d6e089bb
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938859"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>使用 Azure NetApp 文件的解决方案体系结构
本文介绍了可帮助你了解使用 Azure NetApp 文件的解决方案体系结构的最佳做法。  

下图总结了 Azure NetApp 文件提供的解决方案体系结构的类别：

![解决方案体系结构类别](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS 应用和数据库解决方案

本部分提供适用于 Linux OSS 应用程序和数据库的解决方案的参考。 

### <a name="oracle"></a>Oracle

* [Azure NetApp 文件单卷上的 Oracle 数据库性能](performance-oracle-single-volumes.md)
* [Oracle on Azure 部署最佳实践指南使用 Azure NetApp 文件](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM 映像及其在 Microsoft Azure 上的部署：共享存储配置选项](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [将 Azure NetApp 文件与 Oracle Database 配合使用的好处](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows 应用和 SQL Server 解决方案

本部分提供对 Windows 应用程序和 SQL Server 解决方案的参考。

### <a name="file-sharing-and-global-file-caching"></a>文件共享和全局文件缓存

* [构建你自己的 Azure NFS？将 Linux 文件共享 Wrestling 到云中](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [全局文件缓存/Azure NetApp 文件部署](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [与 Azure NetApp 文件一起部署 SMB SQL Server](https://www.youtube.com/watch?v=x7udfcYbibs)
<!-- * [Deploy SQL Server Always-On Failover Cluster over SMB with Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) --> 
<!-- * [Deploy Always-On Availability Groups with Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) --> 

## <a name="sap-on-azure-solutions"></a>Azure 上的 SAP 解决方案

本部分提供对 Azure 解决方案上的 SAP 的参考。 

### <a name="generic-sap-and-sap-netweaver"></a>通用 SAP 和 SAP Netweaver 

* [使用 Azure NetApp 文件 Microsoft Azure 上的 SAP 应用程序](https://www.netapp.com/us/media/tr-4746.pdf)
* [带有适用于 SAP 应用程序的 Azure NetApp 文件的 SUSE Linux Enterprise Server 上 Azure VM 上的 SAP NetWeaver 的高可用性](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Azure Vm 上的 SAP NetWeaver 高可用性，适用于 SAP 应用程序的 Azure NetApp 文件 Red Hat Enterprise Linux](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [适用于 Windows 上的 Azure 虚拟机上的 SAP NetWeaver 高可用性，适用于 SAP 应用程序 (SMB) 的 Azure Vm](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [适用于 SAP 应用程序的 Azure Red Hat Enterprise Linux Vm 上的 SAP NetWeaver 高可用性多 SID 指南](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 虚拟机存储配置](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Red Hat Enterprise Linux 上的 Azure NetApp 文件 SAP HANA 扩展的高可用性](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [在 Azure Vm 上用备用节点 SAP HANA 扩展 SUSE Linux Enterprise Server 上的 Azure NetApp 文件](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [在 Azure Vm 上用备用节点 SAP HANA 扩展 Red Hat Enterprise Linux 上的 Azure NetApp 文件](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)

### <a name="sap-iq-nls"></a>SAP IQ-NLS
*   [使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件部署 SAP IQ-NLS HA 解决方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)

### <a name="sap-tech-community-and-blog-posts"></a>SAP 技术社区和博客文章 

* [Azure NetApp 文件-SAP HANA 备份，以秒为单位](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp 文件–从快照备份还原 HANA 数据库](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp 文件-SAP HANA 通过云同步卸载备份](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [使用 Azure NetApp 文件加快 SAP HANA 系统副本的速度](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [云卷 ONTAP 和 Azure NetApp 文件：简化 SAP HANA 系统迁移](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>虚拟桌面基础结构解决方案

本部分提供了虚拟桌面基础结构解决方案的参考。

### <a name="windows-virtual-desktop"></a>Windows 虚拟桌面

* [将 Azure NetApp 文件与 Windows 虚拟桌面配合使用的好处](solutions-windows-virtual-desktop.md)
* [Windows 虚拟桌面中的 FSLogix 配置文件容器的存储选项](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](../virtual-desktop/create-fslogix-profile-container.md)
* [企业级 Windows 虚拟桌面](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [适用于企业的 Microsoft FSLogix-Azure NetApp 文件最佳实践](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)

## <a name="hpc-solutions"></a>HPC 解决方案

本部分提供高性能计算的参考 (HPC) 解决方案。 

### <a name="generic-hpc"></a>通用 HPC

* [Azure NetApp 文件：充分利用云存储](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Azure Batch 和 Azure NetApp 文件一起运行 MPI 工作负荷](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure 循环云： Azure NetApp 文件上的 CycleCloud HPC 环境](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油和天然气

* [高性能计算 (HPC) ： Azure 中的燃油和天然气](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [在 Azure 上运行容器模拟软件](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>电子设计自动化 (EDA) 

* [使用 Azure NetApp 文件实现电子设计自动化的好处](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud： EDA HPC 实验室与 Azure NetApp 文件](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [适用于半导体行业的 Azure](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analytics

* [Azure NetApp 文件：用于 Microsoft Azure 上的 SAS 网格的新共享文件系统](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)
* [将 Microsoft Azure 与 SAS®配合使用的最佳做法](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Azure 平台服务解决方案

本部分提供 Azure 平台服务的解决方案。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes 服务和 Kubernetes

* [将 Azure NetApp 文件与 Azure Kubernetes 服务集成](../aks/azure-netapp-files.md)
* [Azure 上的 Kubernetes 在 Azure 中的性能和 Azure NetApp 文件](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident-容器的存储 Orchestrator](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Azure Batch 和 Azure NetApp 文件一起运行 MPI 工作负荷](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
