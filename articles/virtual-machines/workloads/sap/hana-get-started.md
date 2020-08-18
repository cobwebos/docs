---
title: 在 Azure 虚拟机上安装 SAP HANA |Microsoft Docs "
description: 在 Azure Vm 上安装 SAP HANA 指南
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
ms.openlocfilehash: f2a62cb08fcce6597f02c080231f5e1808794054
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509954"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>在 Azure 虚拟机上安装 SAP HANA
## <a name="introduction"></a>简介
本指南可帮助你指向正确的资源，以便成功地在 Azure 虚拟机中部署 HANA。 本指南将介绍在 Azure VM 中安装 SAP HANA 之前需要检查的文档资源。 因此，你可以执行正确的步骤，以在 Azure Vm 中使用 SAP HANA 支持的配置。  

> [!NOTE]
> 本指南介绍如何将 SAP HANA 部署到 Azure VM 中。 有关如何将 SAP HANA 部署到 HANA 大型实例的信息，请参阅 [如何在 Azure 上) 安装和配置 SAP HANA (大型实例](./hana-installation.md)。
 
## <a name="prerequisites"></a>先决条件
本指南还假定你熟悉以下内容：
* SAP HANA 和 SAP NetWeaver 以及如何在本地进行安装。
* 如何在 Azure 上安装和运行 SAP HANA 和 SAP 应用程序实例。
* 中记录的概念和过程：
   * 规划 Azure 上的 SAP 部署，包括 Azure 虚拟网络规划和 Azure 存储使用情况。 请参阅 [Azure 虚拟机上的 SAP NetWeaver-规划和实施指南](./planning-guide.md)
   * Azure 中的部署原则以及部署 VM 的方法。 请参阅 [适用于 SAP 的 Azure 虚拟机部署](./deployment-guide.md)
   * [Azure 虚拟机 SAP HANA 高可用性](./sap-hana-availability-overview.md)中所述 SAP HANA 的高可用性概念

## <a name="step-by-step-before-deploying"></a>部署前的分步
本部分列出了在开始在 Azure 虚拟机中安装 SAP HANA 之前需要执行的不同步骤。 枚举订单，并按如下所示进行枚举：

1. 并非所有可能的部署方案在 Azure 上都受支持。 因此，你应在 [Azure 虚拟机支持的方案中查看文档 SAP 工作负荷](./sap-planning-supported-configurations.md) ，了解你在 SAP HANA 部署中所要考虑的情况。 如果未列出该方案，则需要假设尚未对其进行测试，因此不受支持
2. 假设您大致了解 SAP HANA 部署的内存要求，则需要找到一个适合的 Azure VM。 并非所有经 SAP NetWeaver 认证的 Vm （如 [sap 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中所述）都 SAP HANA 认证。 SAP HANA 认证的 Azure Vm 的真实来源是网站 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 以 **S** 开头的单位为 [HANA 大型实例](./hana-overview-architecture.md) 单元，而不是 Azure vm。
3. 对于 SUSE Linux 或 Red Hat Linux，不同的 Azure VM 类型具有不同的最低操作系统版本。 在网站 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)上，需要单击 SAP HANA 认证单位列表中的条目以获取此单位的详细数据。 除了支持的 HANA 工作负荷，列出了 SAP HANA 的那些单元支持的操作系统版本
4. 在操作系统版本中，你需要考虑特定的最低内核版本。 以下 SAP 支持说明中记录了这些最低版本：
    - [SAP 支持说明 #2814271 在 Azure 上 SAP HANA 备份失败，出现校验和错误](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP 支持说明 #2753418 由于计时器回退导致性能下降](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP 支持说明 #2791572 性能下降，因为 Azure 中的 Hyper-v 缺少 VDSO 支持](https://launchpad.support.sap.com/#/notes/2791572)
4. 根据所选虚拟机所支持的操作系统版本，需要检查该操作系统版本是否支持所需的 SAP HANA 版本。 阅读 [SAP 支持说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581) ，了解使用不同操作系统版本 SAP HANA 版本的支持矩阵。
5. 由于你可能找到了 Azure VM 类型、操作系统版本和 SAP HANA 版本的有效组合，因此需要查看 SAP 产品可用性矩阵。 在 SAP 可用性矩阵中，可以了解要对 SAP HANA 数据库运行的 SAP 产品是否受支持。


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>分步 VM 部署和来宾操作系统注意事项
在此阶段中，需要完成部署 VM () 的步骤，以安装 HANA，并最终在安装后优化所选操作系统。

1. 从 Azure 库中选择基本映像。 如果要为 SAP HANA 生成自己的操作系统映像，则需要知道成功的 SAP HANA 安装所需的所有不同包。 否则，建议使用适用于 SAP 的 SUSE 和 Red Hat 映像，或从 Azure 映像库中 SAP HANA。 这些映像包括成功安装 HANA 所需的包。 根据操作系统提供商的支持合同，你需要选择一个映像，你可以在其中自带许可证。 或者选择包含支持的操作系统映像
2. 如果你选择了要求你提供自己的许可证的来宾操作系统映像，则需要将操作系统映像注册到你的订阅，因此，你可以下载并应用最新的修补程序。 此步骤将需要公共 internet 访问权限。 除非在 Azure 中设置的专用实例，例如 SMT 服务器。
3. 确定 VM 的网络配置。 有关详细信息，请参阅文档 [SAP HANA Azure 上的基础结构配置和操作](./hana-vm-operations.md)。 请记住，没有可分配给 Azure 中虚拟网卡的网络吞吐量配额。 因此，将流量定向到不同 Vnic 的唯一目的是基于安全注意事项。 我们相信，通过多个 Vnic 和安全方面的要求，在流量路由的复杂性之间找到一种受支持的折衷机制。
3. 部署并注册 VM 后，请将最新的修补程序应用到操作系统。 已注册到你自己的订阅。 或者，如果你选择的映像包含操作系统支持，则 VM 应具有对修补程序的访问权限。 
4. 应用 SAP HANA 所需的微调。 以下 SAP 支持说明中列出了这些技术：

    - [SAP 支持说明 #2694118-Red Hat Enterprise Linux Azure 上的 HA 外接程序](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP 支持说明 #1984787-SUSE LINUX Enterprise Server 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP 支持说明 #2578899-SUSE Linux Enterprise Server 15：安装说明](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP 支持说明 #2002167 Red Hat Enterprise Linux 7、windows：安装和升级](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置） 
    -  [SAP 支持说明 #2772999 Red Hat Enterprise Linux 3.x：安装和配置](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP 支持说明 #2777782-SAP HANA DB：适用于 RHEL 8 的建议 OS 设置](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP 支持说明 #2455582-Linux：运行用 GCC 1.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP 支持说明 #2382421 优化 HANA 和操作系统级别的网络配置](https://launchpad.support.sap.com/#/notes/2382421)

1. 选择用于 SAP HANA 的 Azure 存储类型。 在此步骤中，需要决定 SAP HANA 安装的存储布局。 你将使用附加的 Azure 磁盘或本机 Azure NFS 共享。 Azure [虚拟机存储配置 SAP HANA](./hana-vm-operations-storage.md)中介绍了可以使用的 azure 存储类型或支持的不同 azure 存储类型的组合。 采用记录为起始点的配置。 对于非生产系统，可以配置较低的吞吐量或 IOPS。 对于生产用途，可能需要配置更多的吞吐量和 IOPS。
2. 使用 M 系列或 Mv2 系列 Vm 时，请确保为包含 DBMS 事务日志或重做日志的卷配置 [Azure 写入加速器](../../how-to-enable-write-accelerator.md) 。 请注意写入加速器中所述的限制。
2. 检查是否在部署) 的 (VM 上启用了 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 。

> [!NOTE]
> 并非各种 sap 微调配置文件中的所有命令，也不能在 Azure 上成功运行。 操作 Vm 的电源模式的命令通常会返回错误，因为无法操作基础 Azure 主机硬件的电源模式。

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>特定于 Azure 虚拟机的分步准备
其中一种 Azure 具体情况是安装 Azure VM 扩展，该扩展可为 SAP 主机代理提供监视数据。 有关此监视扩展安装的详细信息，请参见：

-  [Sap 说明 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) 讨论了 Azure 上的 Linux VM 的 sap 增强型监视 
-  [SAP 说明 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) 讨论了有关 Linux 上的 SAPOSCOL 的信息 
-  [Sap 说明 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) 讨论 Microsoft Azure 上的 sap 的关键监视指标
-  [SAP NetWeaver 的 Azure 虚拟机部署](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 安装
部署 Azure 虚拟机并注册并配置操作系统后，你可以根据 SAP 安装 SAP HANA 安装。 若要获取此文档，请从此 SAP 网站[HANA 资源](https://www.sap.com/products/hana/implementation/resources.html)着手

有关使用 Azure 高级存储或超磁盘的直接附加磁盘的 SAP HANA 横向扩展配置，请参阅文档中的详细信息 [SAP HANA azure 上的基础结构配置和操作](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA 备份的其他资源
有关如何在 Azure Vm 上备份 SAP HANA 数据库的信息，请参阅：
* [Azure 虚拟机上 SAP HANA 的备份指南](./sap-hana-backup-guide.md)
* [文件级别的 SAP HANA Azure 备份](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>后续步骤
阅读文档：

- [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md)
- [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)
