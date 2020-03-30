---
title: 在 Azure 虚拟机上安装 SAP HANA |微软文档
description: 在 Azure VM 上安装 SAP HANA 指南
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123352"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>在 Azure 虚拟机上安装 SAP HANA
## <a name="introduction"></a>介绍
本指南可帮助您指出在 Azure 虚拟机中成功部署 HANA 的正确资源。 本指南将指向在 Azure VM 中安装 SAP HANA 之前需要检查的文档资源。 因此，您将能够执行正确的步骤，以 Azure VM 中支持的 SAP HANA 配置结束。  

> [!NOTE]
> 本指南介绍如何将 SAP HANA 部署到 Azure VM 中。 有关如何将 SAP HANA 部署到 HANA 大型实例中的信息，请参阅[如何在 Azure 上安装和配置 SAP HANA（大型实例）。](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
 
## <a name="prerequisites"></a>先决条件
本指南还假定您熟悉：
* SAP HANA 和 SAP NetWeaver 以及如何在本地进行安装。
* 如何在 Azure 上安装和操作 SAP HANA 和 SAP 应用程序实例。
* 记录在以下方面的概念和程序：
   * 规划 Azure 上的 SAP 部署，其中包括 Azure 虚拟网络规划和 Azure 存储使用情况。 有关[Azure 虚拟机，请参阅 SAP NetWeaver - 规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Azure 中的部署原则以及部署 VM 的方法。 请参阅[SAP 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * SAP HANA 的高可用性概念，如[SAP HANA 中所示，适用于 Azure 虚拟机的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>部署前的分步
在本节中，列出了在 Azure 虚拟机中开始安装 SAP HANA 之前需要执行的不同步骤。 顺序将被枚举，因此应遵循列举如下：

1. Azure 上并非支持所有可能的部署方案。 因此，您应该检查 Azure[虚拟机支持的方案上的文档 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)，了解 SAP HANA 部署时所牢记的方案。 如果未列出方案，则需要假定该方案尚未测试，因此不受支持
2. 假设您对 SAP HANA 部署的内存要求有大致的想法，则需要查找合适的 Azure VM。 并非所有经过 SAP NetWeaver 认证的[VM（如 SAP 支持说明#1928533](https://launchpad.support.sap.com/#/notes/1928533)中所述）均经过 SAP HANA 认证。 SAP HANA 认证 Azure VM 的真相来源是 SAP [HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 以**S**开头的单位是[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)单元，而不是 Azure VM。
3. 不同的 Azure VM 类型具有不同的 SUSE Linux 或红帽 Linux 的最小操作系统版本。 在[SAP HANA 硬件目录中](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，您需要点击 SAP HANA 认证单元列表中的条目，以获取此单元的详细数据。 除了支持的 HANA 工作负载外，还列出了 SAP HANA 的这些单元支持的操作系统版本
4. 在操作系统版本时，需要考虑某些最小内核版本。 这些最低版本记录在这些 SAP 支持说明中：
    - [SAP 支持说明#2814271 SAP HANA 备份在 Azure 上失败，但检查与错误](https://launchpad.support.sap.com/#/notes/2814271)
    - [由于计时器回退，SAP 支持说明#2753418潜在的性能下降](https://launchpad.support.sap.com/#/notes/2753418)
    - [由于 Azure 中缺少对超 V 的 VDSO 支持，SAP 支持说明#2791572性能下降](https://launchpad.support.sap.com/#/notes/2791572)
4. 根据为选择的虚拟机类型支持的操作系统版本，您需要检查该操作系统版本是否支持所需的 SAP HANA 版本。 阅读[SAP 支持说明#2235581，](https://launchpad.support.sap.com/#/notes/2235581)了解具有不同操作系统版本的 SAP HANA 版本的支持矩阵。
5. 由于您可能已经找到了 Azure VM 类型、操作系统版本和 SAP HANA 版本的有效组合，因此您需要签入 SAP 产品可用性矩阵。 在 SAP 可用性矩阵中，您可以了解是否要针对 SAP HANA 数据库运行的 SAP 产品。


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>分步 VM 部署和来宾操作系统注意事项
在此阶段，您需要执行部署 VM 以安装 HANA 并最终在安装后优化所选操作系统的步骤。

1. 从 Azure 库中选择基本映像。 如果要为 SAP HANA 构建自己的操作系统映像，则需要了解成功安装 SAP HANA 所需的所有不同软件包。 否则，建议在 Azure 映像库中使用 SAP 或 SAP HANA 的 SUSE 和红帽映像。 这些映像包括成功安装 HANA 所需的软件包。 根据您与操作系统提供商的支持合同，您需要选择自己携带许可证的映像。 或者，您可以选择包含支持的操作系统映像
2. 如果您选择了需要您自带许可证的来宾操作系统映像，则需要在订阅中注册操作系统映像，以便下载并应用最新的修补程序。 此步骤将需要公共互联网接入。 除非您在 Azure 中设置了 SMT 服务器的专用实例。
3. 决定 VM 的网络配置。 您可以在文档中阅读 SAP [HANA 基础结构配置和 Azure 上的操作中的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)更多信息。 请记住，没有可以分配给 Azure 中的虚拟网卡的网络吞吐量配额。 因此，通过不同的 vNIC 定向流量的唯一目的是基于安全注意事项。 我们相信您能够在通过多个 vNIC 的流量路由的复杂性和安全方面强制实施的要求之间找到可支持的折衷方案。
3. 部署和注册 VM 后，将最新的修补程序应用于操作系统。 使用您自己的订阅注册。 或者，如果您选择了包含操作系统支持的映像，VM 应已经有权访问修补程序。 
4. 应用 SAP HANA 所需的调子。 这些调子列在这些 SAP 支持说明中：

    - [#2694118 SAP 支持说明 - Azure 上的红帽企业 Linux HA 附加组件](https://launchpad.support.sap.com/#/notes/2694118)
    - [#1984787 SAP 支持说明 - SUSE LINUX 企业服务器 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP 支持说明#2578899 - SUSE Linux 企业服务器 15：安装说明](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP 支持说明#2002167 - 红帽企业 Linux 7.x：安装和升级](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置） 
    -  [SAP 支持说明#2772999 - 红帽企业 Linux 8.x：安装和配置](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP 支持说明#2777782 - SAP HANA DB：RHEL 8 的推荐操作系统设置](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP 支持说明#2455582 - Linux：运行使用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP 支持说明#2382421 - 在 HANA 和操作系统级别上优化网络配置](https://launchpad.support.sap.com/#/notes/2382421)

1. 选择 SAP HANA 的 Azure 存储类型。 在此步骤中，您需要决定 SAP HANA 安装的存储布局。 您将使用附加的 Azure 磁盘或本机 Azure NFS 共享。 SAP [HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中记录了支持或支持的 Azure 存储类型以及可以使用的不同 Azure 存储类型的组合。 以记录为起点的配置为例。 对于非生产系统，您可能能够配置较低的吞吐量或 IOPS。 出于生产目的，您可能需要配置更多的吞吐量和 IOPS。
2. 请确保在使用 M 系列或 Mv2 系列 VM 时为包含 DBMS 事务日志的卷配置[Azure 写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或重做日志。 请注意已记录的写入加速器的限制。
2. 检查是否已在部署的 VM 上启用[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。

> [!NOTE]
> 不同树智调配置文件或注释中描述的所有命令都可能在 Azure 上成功运行。 操作 VM 的电源模式的命令通常会返回时出现错误，因为无法操作基础 Azure 主机硬件的电源模式。

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>特定于 Azure 虚拟机的分步准备
Azure 的一个细节是安装 Azure VM 扩展，该扩展为 SAP 主机代理提供监视数据。 有关安装此监视扩展的详细信息记录在：

-  [SAP 注释 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)讨论在 Azure 上使用 Linux VM 进行 SAP 增强的监视 
-  [SAP 注释 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)讨论有关 Linux 上的 SAPOSCOL 的信息 
-  [SAP 注释 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)讨论 Microsoft Azure 上 SAP 的关键监视指标
-  [适用于 SAP NetWeaver 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 安装
部署 Azure 虚拟机并注册和配置操作系统后，可以根据 SAP 安装安装 SAP HANA。 作为获取本文档的良好开端，从此 SAP 网站[HANA 资源](https://www.sap.com/products/hana/implementation/resources.html)开始

对于使用直接连接的 Azure 高级存储或超磁盘磁盘的 SAP HANA 横向扩展配置，请阅读文档中的[SAP HANA 基础结构配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA 备份的其他资源
有关如何在 Azure VM 上备份 SAP HANA 数据库的信息，请参阅：
* [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [文件级别的 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>后续步骤
阅读文档：

- [Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





