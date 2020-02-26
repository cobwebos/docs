---
title: Azure 上的 SAP： Azure Vm 支持的方案
description: Azure 虚拟机支持 SAP 工作负荷的方案
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28a9de63bb04a95fc2e655b05727963feaa3ec40
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599179"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 虚拟机支持的方案中的 SAP 工作负荷
在 Azure 中设计 SAP NetWeaver、Business one、`Hybris` 或 S/4HANA 系统体系结构可为各种体系结构和工具提供很多不同的机会，以实现可缩放、高效和高度可用的部署。 尽管依赖于所使用的操作系统或 DBMS，但存在一些限制。 此外，Azure 中不支持在本地支持的所有方案。 本文档将通过专门使用 Azure Vm 提供支持的非高可用性配置和高可用性配置和体系结构。 对于[Hana 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)支持的方案，请查看文章[适用于 hana 大型实例的支持方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)。 


## <a name="2-tier-configuration"></a>双层配置
SAP 2 层配置被认为是在 SAP DBMS 和应用程序层的组合层的基础上构建的，它在相同的服务器或 VM 单元上运行。 第二层被视为用户界面层。 对于双层配置，DBMS 和 SAP 应用程序层共享 Azure VM 的资源。 因此，你需要以不会争用资源的方式配置不同组件。 还需要小心，不要过度订阅 VM 的资源。 此类配置不提供任何高可用性，超出了所涉及的不同 Azure 组件的[Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)。

此类配置的图形表示形式如下所示：

![简单2层配置](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

对于用于生产和非生产事例 SQL Server、Oracle、Db2、maxDB 和 SAP ASE 的 DBMS 系统，Windows、Red Hat、SUSE 和 Oracle Linux 支持这种配置。 对于作为 DBMS 的 SAP HANA，这种类型的配置仅支持非生产用例。 这包括[AZURE HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)的部署用例。
对于 Azure 上支持的所有 OS/DBMS 组合，支持这种配置。 但是，您必须设置 DBMS 和 SAP 组件的配置，使 DBMS 和 SAP 组件不会争用内存和 CPU 资源，因而超出了可用的物理资源。 这需要通过限制 DBMS 允许分配的内存来完成。 还需要限制应用程序实例上的 SAP 扩展内存。 还需要监视 VM 整体的 CPU 使用情况，以确保组件不会将 CPU 资源最大化。 

> [!NOTE]
> 对于生产 SAP 系统，我们建议其他高可用性和最终的灾难恢复配置，如本文档后面所述


## <a name="3-tier-configuration"></a>3层配置
在此类配置中，你需要将 SAP 应用程序层和 DBMS 层分为不同的虚拟机。 通常，对于较大的系统，以及在 SAP 应用程序层的资源上更加灵活的原因，通常会这样做。 在最简单的设置中，除了所涉及的不同 Azure 组件的[Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)外，没有高可用性。 

图形表示形式如下所示：

![简单2层配置](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

对于用于生产和非生产事例 SQL Server、Oracle、Db2、SAP HANA、maxDB 和 SAP ASE 的 DBMS 系统，Windows、Red Hat、SUSE 和 Oracle Linux 支持这种类型的配置。 这是[AZURE HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)的默认部署配置。 为简化，我们没有在 sap 中心服务和 sap 应用程序层中的 SAP 对话实例之间进行区分。 在这个简单的3层配置中，SAP 中心服务不会提供高可用性保护。

> [!NOTE]
> 对于生产 SAP 系统，我们建议其他高可用性和最终的灾难恢复配置，如本文档后面所述


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>每个 VM 或 HANA 大型实例单元的多个 DBMS 实例
在此配置类型中，你可以为每个 Azure VM 或 HANA 大型实例单元托管多个 DBMS 实例。 这种动机的工作方式是，使用较少的操作系统来维护和降低成本。 其他动机可以通过在多个 DBMS 实例之间共享更大的 VM 或 HANA 大型实例单元的资源，提高灵活性和效率。 到目前为止，这些配置主要用于非生产系统。

如下所示的配置：

![一个单元中有多个 DBMS 实例](./media/sap-planning-supported-configurations/multiple-database-instances.png)

支持这种类型的 DBMS 部署：

- Windows 上的 SQL Server
- IBM Db2。 在[多个实例（Linux、UNIX）](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)一文中查找详细信息
- 对于 Oracle。 有关详细信息，请参阅[sap 支持说明 #1778431](https://launchpad.support.sap.com/#/notes/1778431)和相关 SAP 说明
- 对于 SAP HANA，一个虚拟机上的多个实例，SAP 调用此部署方法 MCOS。 有关详细信息，请参阅 SAP 文章[一台主机上的多个 SAP HANA 系统（MCOS）](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/en-US/b2751fd43bec41a9a14e01913f1edf18.html)

在一个主机上运行多个数据库实例时，需要确保不同的实例不会争用资源，因而超出了 VM 的物理资源限制。 对于需要将 VM 共享的任何人都可以分配的内存上限的内存，尤其如此。 对于不同数据库实例可以利用的 CPU 资源也可能如此。 提到的所有 DBMS 都具有允许在实例级别限制内存分配和 CPU 资源的配置。
为了让 Azure Vm 支持此类配置，预计用于不同实例管理的数据库的数据和日志/重做日志文件的磁盘或卷是独立的。 换句话说，不应将不同 DBMS 实例管理的数据库的数据或日志/重做日志文件共享到相同的磁盘或卷。 

HANA 大型实例的磁盘配置已配置，并在[Hana 大型实例支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos)中进行了详细说明。 

> [!NOTE]
> 对于生产 SAP 系统，我们建议更高的高可用性和最终的灾难恢复配置，如本文档后面所述。 此文档稍后介绍的高可用性配置不支持具有多个 DBMS 实例的 Vm。


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>一个虚拟机中的多个 SAP 对话框实例
在许多情况下，多个对话框实例部署在裸机服务器上，甚至部署在在私有云中运行的虚拟机中。 此类配置的原因是要将某些 SAP 对话框实例定制为特定的工作负荷、业务功能或工作负荷类型。 将这些实例隔离到不同 Vm 中的原因是操作系统维护和操作的工作量。 或者，在很多情况下，VM 的宿主或操作员要求按每个 VM 的每月费用（运行和根据）。 在 Azure 中，在单个 VM 中托管多个 SAP 对话框实例的方案，在 Windows、Red Hat、SUSE 和 Oracle Linux 的操作系统上支持生产和非生产目的。 如果多个 SAP 应用程序服务器实例正在单个 VM 上运行，则应设置 SAP 内核参数 PHYS_MEMSIZE，在 Windows 和新式 Linux 内核上可用。 此外，建议限制操作系统上的 SAP 扩展内存扩展，如 Windows，实现 SAP 扩展内存的自动增长。 可以通过 `em/max_size_MB`的 SAP 配置文件参数来完成此操作。

在 Azure Vm 中运行多个 SAP 对话框实例的3层配置如下所示：

![一个单元中有多个 DBMS 实例](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

为简化，我们没有在 sap 中心服务和 sap 应用程序层中的 SAP 对话实例之间进行区分。 在这个简单的3层配置中，SAP 中心服务不会提供高可用性保护。 对于生产系统，不建议保持 SAP 中心服务不受保护。 有关此类多 SID 配置的详细信息，请参阅本文档后面部分的关于 SAP 中心实例的多 SID 配置和此类多 SID 配置的高可用性。

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS 层的高可用性保护
当你希望部署 SAP 生产系统时，你需要考虑热备用配置类型。 尤其是在 SAP HANA，需要先将数据加载到内存中，然后才能获得完整的性能和可伸缩性，Azure 服务修复不是实现高可用性的理想度量。 

通常，Microsoft 仅支持 docs.microsoft.com 中的 "SAP 工作负荷" 部分下所述的高可用性配置和软件包。 可以在 SAP 说明[#1928533](https://launchpad.support.sap.com/#/notes/1928533)中阅读相同的语句。 Microsoft 不会为 Microsoft 在 SAP 工作负荷中记录的其他高可用性第三方软件框架提供支持。 在这种情况下，高可用性框架的第三方供应商是高可用性配置的支持方，需要将你作为客户参与到支持流程中。 本文将对异常进行说明。 

通常，Microsoft 支持 Azure Vm 或 HANA 大型实例单元上的一组有限的高可用性配置。 有关 HANA 大型实例的受支持方案，请阅读文档[支持的 Hana 大型实例的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)。

对于 Azure Vm，DBMS 级别支持以下高可用性配置：

- SAP HANA 基于 SUSE 和 Red Hat 上的 Linux Pacemaker 进行系统复制。 请参阅详细文章：
    - [SUSE Linux Enterprise Server 上的 Azure Vm 的高可用性 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Red Hat Enterprise Linux 上的 Azure Vm 的高可用性 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- 使用 SUSE 和 Red Hat 上的[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)SAP HANA 扩展 n + m 配置。 以下文章中列出了详细信息：
    - [使用 SUSE Linux Enterprise Server} 上的 Azure NetApp 文件在 Azure Vm 上通过备用节点部署 SAP HANA 扩展系统](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [使用 Red Hat Enterprise Linux 上的 Azure NetApp 文件在 Azure Vm 上使用备用节点部署 SAP HANA 扩展系统](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 基于 Windows 横向扩展文件服务 SQL Server 故障转移群集。 尽管生产系统的建议是使用 SQL Server Always On 而不是聚类分析。 SQL Server Always On 可以使用单独的存储提供更好的可用性。 本文介绍了详细信息： 
    - [在 Azure 虚拟机上配置 SQL Server 故障转移群集实例](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- 适用于 Azure 上的 SQL Server 的 Windows 操作系统支持 SQL Server Always On。 这是 Azure 上生产 SQL Server 实例的默认建议。 有关详细信息，请参阅以下文章：
    - [介绍 Azure 虚拟机上的 SQL Server Always On 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。
    - [在位于不同区域的 Azure 虚拟机上配置 Always On 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)。
    - [在 Azure 中为 Always On 可用性组配置负载均衡器](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)。
- 适用于 Windows 和 Oracle Linux 的 Oracle 数据防护。 可在此文中找到 Oracle Linux 的详细信息：
    - [在 Azure Linux 虚拟机上实现 Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- 此处提供了有关 suse 和 rhel 的 suse 和 RHEL 详细文档中的 IBM Db2 HADR：
    - [与 Pacemaker SUSE Linux Enterprise Server 上的 Azure Vm 上的 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Red Hat Enterprise Linux Server 上的 Azure Vm 上的 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- 这些文档中详细说明的 SAP ASE 和 SAP maxDB 配置：
    - [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [Azure VM 上的 SAP MaxDB、liveCache 和内容服务器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- 中详细介绍了 HANA 大型实例的高可用性方案：
    - [HANA 大型实例支持的方案-HSR 与 STONITH 实现高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [HANA 大型实例支持的方案-主机自动故障转移（1 + 1）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> 对于上述方案，我们不支持在一个 VM 中配置多个 DBMS 实例。 意思是，在每种情况下，每个 VM 只能部署一个数据库实例，并通过所述的高可用性方法对其进行保护。 此时**不**支持在同一 Windows 或 Pacemaker 故障转移群集下保护多个 DBMS 实例。 此外，仅每个 VM 部署案例的单个实例支持 Oracle 数据防护。 


![DBMS HA 配置](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

依赖于 DBMS a/或操作系统，Azure 负载均衡器等组件可能需要也可能不是解决方案体系结构的一部分。 

专用于 maxDB，存储配置需要不同。 在 maxDB 中，数据和日志文件需要位于共享存储上，以实现高可用性配置。 仅在 maxDB 的情况下，支持共享存储以实现高可用性。 对于所有其他 DBMS，每个节点的单独存储堆栈是唯一受支持的磁盘配置。

还知道存在其他高可用性框架，也知道在 Microsoft Azure 上运行这些框架。 但是，Microsoft 并未测试这些框架。 如果要使用这些框架构建高可用性配置，则需要使用该软件的提供程序来执行以下操作：

- 开发部署体系结构
- 体系结构的部署
- 对体系结构的支持

> [!IMPORTANT]
> Microsoft Azure 市场提供各种软设备，这些设备在 Azure 本机存储基础上提供存储解决方案。 这些软设备还可用于创建 NFS 共享，理论上可以在需要备用节点的 SAP HANA 扩展部署中使用这些设备。 由于各种原因，Microsoft 和 Azure 上的 SAP 部署的任何 DBMS 部署都不支持这些存储软设备。 SMB 共享上的 DBMS 部署目前不受支持。 NFS 共享上的 DBMS 部署仅限于[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)上的 NFS 4.1 共享。


## <a name="high-availability-for-sap-central-service"></a>SAP 中心服务的高可用性
SAP 中心服务是 SAP 配置的第二个故障点。 因此，你还需要保护这些中心服务过程。 SAP 工作负荷的支持和记录的产品/服务的读取方式如下：

- Windows 故障转移群集服务器使用 Windows 横向扩展文件服务进行 sapmnt 和全局传输目录。 有关详细信息，请参阅以下文章：
    - [使用 Azure 中的文件共享在 Windows 故障转移群集上群集 SAP ASCS/SCS 实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- 基于 sapmnt 和全局传输目录的[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)，使用 SMB 共享的 Windows 故障转移群集服务器。 有关详细信息，请在以下文章中列出：
    - [适用于 SAP 应用程序的 azure 虚拟机上的 SAP NetWeaver 的高可用性，适用于 SAP 应用程序的 Azure NetApp 文件（SMB）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- 基于 SIOS `Datakeeper`的 Windows 故障转移群集服务器。 尽管 Microsoft 记录了 Microsoft 的支持关系，但在使用此解决方案时，你可以与 sios 支持联系。 有关详细信息，请参阅以下文章：
    - [使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [为 SAP ASCS/SCS 使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- 通过使用两个 SUSE Vm 和 `drdb` 进行文件复制，在 SUSE 操作系统上 Pacemaker。 文章中记录了详细信息
    - [SUSE Linux Enterprise Server for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [SUSE Linux Enterprise Server 上的 Azure Vm 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- 利用[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)提供的 NFS 共享 Pacemaker SUSE 操作系统。 详细信息记录在
    - [Azure Vm 上的 SAP NetWeaver 高可用性，适用于 SAP 应用程序的 Azure NetApp 文件 SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- 在 `glusterfs` 群集上托管有 NFS 共享的 Red Hat 操作系统上的 Pacemaker。 有关详细信息，请参阅文章
    - [Azure 虚拟机在 Red Hat Enterprise Linux 上为 SAP NetWeaver 提供的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure Vm `GlusterFS`](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- 在[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)上托管有 NFS 共享的 Red Hat 操作系统上的 Pacemaker。 有关详细信息，请参阅
    - [适用于 sap NetWeaver 的 azure 虚拟机高可用性，适用于 SAP 应用程序的 Azure NetApp 文件 Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

在列出的解决方案中，你需要与 SIOS 之间的支持关系，以支持 `Datakeeper` 的产品，并在出现问题时直接与 SIOS 进行联系。 取决于你对 Windows、Red Hat 和/或 SUSE 操作系统的授权方式，你可能还需要与操作系统提供商签订支持合同，才能完全支持列出的高可用性配置。

此配置还可以像下面这样显示：

![DBMS 和 ASCS HA 配置](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

图的右侧显示了高度可用的 SAP 中心服务。 除了使用故障转移群集框架保护的 SAP 中心服务在出现问题时进行故障转移之外，还需要具有高可用 NFS 或 SMB 共享或 Windows 共享磁盘，以确保 sapmnt 和全局传输目录独立于单个 VM 是否存在。 其他一些解决方案（如 Windows 故障转移群集服务器和 Pacemaker）将需要 Azure 负载均衡器将流量定向到正常的节点或将其直接定向到正常的节点。

在显示的列表中，没有提及 Oracle Linux 的操作系统。 Oracle Linux 不支持作为群集框架的 Pacemaker。 如果要在 Oracle Linux 上部署 SAP 系统并且需要 Oracle Linux 的高可用性框架，则需要与第三方供应商合作。 其中一个供应商通过其适用于 Linux 的保护套件进行 SIOS，Azure 上的 SAP 支持这些套件。 有关详细信息，请阅读 SAP 说明 #1662610-适用于适用于[Linux 的 SIOS 保护套件的详细](https://launchpad.support.sap.com/#/notes/1662610)信息。



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>上面列出的 SAP 中心服务方案支持的存储
由于仅有一个 Azure 存储类型的子集提供高可用性 NFS 或 SMB 共享，因此，在 SAP 中心服务群集方案中的使用质量就是受支持的存储类型列表

- Windows 横向扩展文件服务器的 windows 故障转移群集服务器可以部署在除 Azure NetApp 文件之外的所有本机 Azure 存储类型上。 不过，建议使用高级存储，因为吞吐量和 IOPS 具有优异的服务级别协议。
- Azure netapp 文件上支持带有 SMB 的 Windows 故障转移群集服务器。 此时**不**支持 Azure 文件服务上的 SMB 共享。
- 基于 SIOS `Datakeeper` 的 windows 故障转移群集服务器可以部署在除 Azure NetApp 文件以外的所有本机 Azure 存储类型上。 不过，建议使用高级存储，因为吞吐量和 IOPS 具有优异的服务级别协议。
- 使用 Azure NetApp 文件上的 NFS 共享的 SUSE 或 Red Hat Pacemaker 在 Azure NetApp 文件上受支持。 
- 使用本地 Azure 存储类型（Azure NetApp 文件除外）支持在两个 Vm 之间使用 `drdb` 配置的 SUSE Pacemaker。 不过，建议使用高级存储，因为吞吐量和 IOPS 具有优异的服务级别协议。
- 对于使用 `glusterfs` 提供 NFS 共享的 Red Hat Pacemaker，支持使用 Azure NetApp 文件以外的本机 Azure 存储类型。 不过，建议使用高级存储，因为吞吐量和 IOPS 具有优异的服务级别协议。

> [!IMPORTANT]
> Microsoft Azure 市场提供各种软设备，这些设备在 Azure 本机存储基础上提供存储解决方案。 这些软设备可用于创建 NFS 或 SMB 共享，理论上还可以在故障转移群集 SAP 中心服务中使用这些设备。 Microsoft 不直接支持这些解决方案。 如果决定使用此类解决方案来创建 NFS 或 SMB 共享，则需要由在存储软设备中拥有该软件的第三方提供对 SAP 中心服务配置的支持。


## <a name="multi-sid-sap-central-services-failover-clusters"></a>多 SID SAP 中心服务故障转移群集
为了减少大型 SAP 环境中所需的 Vm 数量，SAP 允许在故障转移群集配置中运行多个不同 SAP 系统的 SAP 中心服务实例。 假设有30个或更多 NetWeaver 或 S/4HANA 生产系统。 如果没有多 SID 群集，这些配置需要30个或更多个 Windows 或 Pacemaker 故障转移群集配置中的60或更多 Vm。 除了所需的 DBMS 故障转移群集。 在故障转移群集配置中跨两个节点部署多个 SAP 中心服务可显著减少 Vm 的数量。 但是，在一个双节点群集配置上部署多个 SAP 中心服务实例也有一些缺点。 群集配置中的单个 VM 周围的问题适用于多个 SAP 系统。 在群集配置中运行的来宾 OS 维护需要更多协调，因为多个生产 SAP 系统会受到影响。 SAP LaMa 等工具不支持其系统克隆过程中的多 SID 群集。

在 Azure 上，使用 ENSA1 和 ENSA2 的 Windows 操作系统支持多 SID 群集配置。 建议不要将较旧的排队复制服务结构（ENSA1）与一个多 SID 群集上的新体系结构（ENSA2）组合在一起。 有关此类体系结构的详细信息，请参见

- [使用 Azure 上的 Windows Server 故障转移群集和共享磁盘实现 SAP ASCS/SCS 实例多 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [在 Azure 上使用 Windows Server 故障转移群集和文件共享实现 SAP ASCS/SCS 实例的多 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

对于 SUSE，还支持基于 Pacemaker 的多 SID 群集。 到目前为止，支持该配置：

- 最多五个 SAP ASCS/SCS 实例
- 旧的排队复制服务器 ice 体系结构（ENSA1）
- 双节点 Pacemaker 群集配置

[适用于 sap 应用程序的 Azure SUSE Linux Enterprise Server vm 上的 Sap NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)记录了此配置多 SID 指南

具有排队复制服务器来说的多 SID 群集类似于

![DBMS 和 ASCS HA 配置](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA 扩展方案
[SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中列出了 HANA 认证的 Azure vm 的子集 SAP HANA 扩展方案。 列 "聚类" 中标记为 "是" 的所有 Vm 均可用于 OLAP 或 S/4HANA 横向扩展。Azure 存储类型支持无备用配置： 

- Azure 高级存储，包括/hana/log 卷的 Azure 写入加速器
- [超磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

在 Azure NetApp 文件上托管的 NFS 共享上，仅支持通过备用节点 SAP HANA 的 OLAP 或 S/4HANA 的扩展配置。

有关包含或不包含备用节点的精确存储配置的详细信息，请查看以下文章：

- [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure Vm 上使用备用节点部署 SAP HANA 扩展系统](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [使用 Red Hat Enterprise Linux 上的 Azure NetApp 文件在 Azure Vm 上使用备用节点部署 SAP HANA 扩展系统](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [SAP 支持说明 #2080991](https://launchpad.support.sap.com/#/notes/2080991)

有关 HANA 大型实例支持的 HANA 横向扩展配置的详细信息，请参阅以下文档：

- [适用于 HANA 大型实例的支持方案通过备用扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [HANA 大型实例支持的方案横向扩展，无需等待](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>灾难恢复方案
支持多种灾难恢复方案。 我们将灾难体系结构定义为体系结构，该体系结构应补偿网格中的完整 Azure 区域。 这意味着，我们需要将灾难恢复目标视为不同的 Azure 区域，作为运行 SAP 环境的目标。 在 DBMS 层和非 DBMS 层中分离方法和配置。 

### <a name="dbms-layer"></a>DBMS 层
对于 DBMS 层，支持使用 DBMS 本机复制机制的配置，例如 Always On、Oracle 数据防护、Db2 HADR、SAP ASE Always On 或 HANA 系统复制。 在这种情况下，复制流必须是异步的，而不是在单个 Azure 区域中部署的典型高可用性方案中进行同步。 有关此类受支持的 DBMS 灾难恢复配置的典型示例，请参阅[SAP HANA 跨 Azure 区域的可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions)一文。 此部分中的第二个图形描述了使用 HANA 作为示例的方案。 SAP 应用程序支持的主数据库都可以在这种情况下进行部署。

支持使用较小的 VM 作为灾难恢复区域中的目标实例，因为该 VM 不会遇到完整的工作负荷流量。 为此，需要注意以下事项：

- 较小的 VM 类型不允许比小型 Vm 连接的多个磁盘
- 较小的 Vm 的网络和存储吞吐量更少
- 在一个 Azure 可用性集中收集不同 Vm 时，或在 M 系列系列与 Mv2 Vm 系列之间发生重新调整大小时，跨 VM 系列重新调整大小可能是一个问题。
- 数据库实例的 CPU 和内存消耗，能够以最小延迟和足够的 CPU 和内存资源来接收更改流，以将这些更改应用于数据的最小延迟  

可在[此处](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)找到有关不同 VM 大小的限制的更多详细信息 

部署 DR 目标的另一种受支持的方法是在运行非生产 SAP 实例的非生产 DBMS 实例的 VM 上安装第二个 DBMS 实例。 这可能有点困难，因为需要确定应该在 DR 方案中充当主实例的特定目标实例需要的内存、CPU 资源、网络带宽和存储带宽。 尤其是在 HANA 中，强烈建议您配置在共享主机上充当 DR 目标的实例，使数据不会预先加载到 DR 目标实例中。

对于 HANA 大型实例灾难恢复方案，请检查以下文档：

- [使用存储复制进行灾难恢复的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [使用存储复制的单节点，含 DR （多用途）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [使用存储复制的单节点，含 DR （多用途）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [通过存储复制实现 HSR 和灾难恢复的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [使用存储复制进行 DR 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [使用 HSR 进行灾难恢复的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [单节点 HSR 到 DR （成本优化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [HSR 的高可用性和灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [HSR 的高可用性和灾难恢复（成本优化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [使用 HSR 通过 DR 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> 尚未针对 SAP 工作负荷下的 DBMS 部署测试[Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)的使用情况。 因此，SAP 系统的 DBMS 层目前不支持这种情况。 Microsoft 和未列出的 SAP 的其他复制方法不受支持。 使用第三方软件在不同的 Azure 区域之间复制 SAP 系统的 DBMS 层时，需要由软件供应商提供支持，且不受 Microsoft 和 SAP 支持渠道支持。 
 
## <a name="non-dbms-layer"></a>非 DBMS 层
对于 SAP 应用程序层和最终的共享或存储位置，客户可以利用这两种主要方案：

- 第二个 Azure 区域中的灾难恢复目标不用于任何生产或非生产目的。 在这种情况下，将不会部署充当灾难恢复目标的 Vm，并且映像和对生产 SAP 应用程序层映像的更改会复制到灾难恢复区域。 可以[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview)执行此类任务的功能。 Azure Site Recovery 支持如下所示的 Azure 到 Azure 复制方案。 
- 灾难恢复目标是非生产系统实际使用的虚拟机。 整个 SAP 布局分布在两个不同的 Azure 区域中，生产系统通常位于一个区域，另一个区域中的非生产系统中。 在许多客户部署中，客户具有与生产系统等效的非生产系统。 客户在应用程序层的非生产系统上预先安装了生产应用程序实例。 在发生故障转移的情况下，非生产实例将关闭，生产 Vm 的虚拟名称移到非生产 Vm （在 DNS 中分配新 IP 地址后），预安装的生产实例将入门

### <a name="sap-central-services-clusters"></a>SAP 中心服务群集
使用共享磁盘（Windows）、SMB 共享（Windows）或 NFS 共享的 SAP 中心服务群集更难复制。 在 Windows 端，Windows 存储复制是一种可能的解决方案。 在 Linux rsync 上，这是一个可行的解决方案。



## <a name="non-supported-scenario"></a>不受支持的方案
存在一系列方案，Azure 体系结构上的 SAP 工作负荷不支持此方案。 **不受支持**意味着 SAP 和 Microsoft 将不能支持这些配置，并且需要推迟到最终涉及的第三方（提供软件来建立此类体系结构）。 其中两个类别为：

- 存储软设备： Azure marketplace 中提供了许多存储软设备。 一些供应商提供了有关如何在 Azure 上使用与 SAP 软件相关的存储软设备的文档。 需要由这些存储软设备的供应商提供涉及此类存储软设备的配置或部署的支持。 [SAP 支持说明](https://launchpad.support.sap.com/#/notes/2015553)中也提供了此事实 #2015553
- 高可用性框架：对于 Azure 上的 SAP 工作负荷，仅支持 Pacemaker 和 Windows Server 故障转移群集。 如前文所述，Microsoft 介绍并记录了 SIOS `Datakeeper` 的解决方案。 尽管如此，`Datakeeper` 的 SIOS 组件都需要通过 SIOS 作为提供这些组件的供应商提供支持。 SAP 还在各种 SAP 说明中列出了其他经过认证的高可用性框架。 其中一些已由第三方供应商提供 Azure 认证。 然而，使用这些产品的配置支持必须由产品供应商提供。 不同的供应商在 SAP 支持过程中具有不同的集成。 在决定在 Azure 上部署的 SAP 配置中使用该产品之前，应阐明哪种支持流程最适合特定的供应商。
- 除了 maxDB 之外，不支持数据库文件驻留在共享磁盘上的共享磁盘群集。 对于其他所有数据库，支持的解决方案是使用单独的存储位置，而不是 SMB 或 NFS 共享或共享磁盘来配置高可用性方案

不受支持的其他方案包括以下方案：

- 在 sap 应用层和 sap DBMS 层之间引入更大网络延迟的部署方案，如 NetWeaver、S/4HANA 和 `Hybris`中所示。 这包括：
    - 在本地部署某个层，而将另一层部署在 Azure 中
    - 在不同于 DBMS 层的 Azure 区域中部署系统的 SAP 应用程序层
    - 将数据中心内的一层部署到 azure 和 Azure 中的另一层，但 Azure 本机服务提供此类体系结构模式除外
    - 在 SAP 应用程序层和 DBMS 层之间部署网络虚拟设备
    - 利用存储在与 Azure 数据中心共存的数据中心中，用于 SAP DBMS 层或 SAP 全局传输目录
    - 部署两个具有两个不同云供应商的层。 例如，在 Oracle 云基础结构中部署 DBMS 层，在 Azure 中部署应用程序层
- 多实例 HANA Pacemaker 群集配置
- 使用共享磁盘的 windows 群集配置，在和上通过 SOFS 或 SMB 在 Windows 上支持 SAP 数据库。 相反，我们建议使用特定数据库的本机高可用性复制，并使用单独的存储堆栈
- 在 Linux 上部署受支持的 SAP 数据库，数据库文件位于和的 NFS 共享上，但 SAP HANA
- 将 Oracle DBMS 部署在任何其他来宾操作系统上，而不是 Windows 和 Oracle Linux。 另请参阅[SAP 支持说明 #2039619](https://launchpad.support.sap.com/#/notes/2039619)

未进行测试，因此没有经验的应用场景，如：

- Azure Site Recovery 复制 DBMS 层虚拟机。 因此，建议使用数据库本机异步复制功能，以实现可能的灾难恢复配置
 

## <a name="next-steps"></a>后续步骤
阅读[SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的后续步骤




  



