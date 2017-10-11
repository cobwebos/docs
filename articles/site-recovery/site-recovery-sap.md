---
title: "使用 Azure Site Recovery 保护多层 SAP NetWeaver 应用程序部署 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 保护 SAP NetWeaver 应用程序部署"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: manayar
ms.openlocfilehash: 951980eeba61e53c983d5b23c301c81eee9528bd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>使用 Azure Site Recovery 保护多层 SAP NetWeaver 应用程序部署

大多数大型和中型 SAP 部署具有某种形式的灾难恢复解决方案。  随着更多核心业务流程迁移到 SAP 等应用程序中，可靠和可测试的灾难恢复解决方案的重要性也越来越高。  Azure Site Recovery 已经过测试并与 SAP 应用程序集成，超过大多数本地灾难恢复解决方案的功能，并且总拥有成本 (TCO) 要低于竞争对手解决方案。
使用 Azure Site Recovery 可以：
* 将组件复制到 Azure，以便保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
* 将组件复制到其他 Azure 数据中心，以便保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
* 使用站点恢复将 SAP 部署迁移到 Azure，从而简化云迁移。
* 通过创建一个按需生产克隆来测试 SAP 应用程序，简化 SAP 项目的升级、测试和原型制作。

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 保护 SAP NetWeaver 应用程序部署。 本文介绍通过使用 Azure Site Recovery 复制到其他 Azure 数据中心来保护 Azure 上的三层 SAP NetWeaver 部署的最佳做法、支持的方案和配置以及如何执行故障转移（测试故障转移（灾难恢复演练）和实际故障转移）。


## <a name="prerequisites"></a>先决条件
在开始之前，请确保了解以下知识：

1. [将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md)
2. 如何[设计恢复网络](site-recovery-azure-to-azure-networking-guidance.md)
3. [执行到 Azure 的测试故障转移](azure-to-azure-walkthrough-test-failover.md)
4. [执行到 Azure 的故障转移](site-recovery-failover.md)
5. 如何[复制域控制器](site-recovery-active-directory.md)
6. 如何[复制 SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支持的方案
使用 Azure Site Recovery 可以为以下方案实现灾难恢复解决方案：
* 在一个 Azure 数据中心内运行的 SAP 系统复制到其他 Azure 数据中心（Azure 到 Azure 灾难恢复），如[此处](https://aka.ms/asr-a2a-architecture)所构建。
* 在本地 VMWare（或物理）服务器上运行的 SAP 系统复制到 Azure 数据中心内的灾难恢复站点（VMware 到 Azure 灾难恢复），这需要一些附加组件，如[此处](https://aka.ms/asr-v2a-architecture)所构建。
* 在本地 Hyper-V 上运行的 SAP 系统复制到 Azure 数据中心内的灾难恢复站点（Hyper-V 到 Azure 灾难恢复），这需要一些附加组件，如[此处](https://aka.ms/asr-h2a-architecture)所构建。

本文档使用第一种情形（Azure 到 Azure 灾难恢复）演示 Azure Site Recovery 的 SAP 灾难恢复功能。 由于 Azure Site Recovery 复制是应用程序不可知的，因此介绍的过程应该也适用于其他方案。

### <a name="required-foundation-services"></a>所需基础服务
本文档方案是在部署了以下基础服务的情况下部署的：
* ExpressRoute 或站点到站点虚拟专用网 (VPN)
* 至少有一个在 Azure 中运行的 Active Directory 域控制器和 DNS 服务器

建议在部署 Azure Site Recovery 之前建立以上基础结构。


## <a name="typical-sap-application-deployment"></a>典型 SAP 应用程序部署
大型 SAP 客户通常部署 6 到 20 个不同的 SAP 应用程序。  这些应用程序中的大多数基于 SAP NetWeaver ABAP 或 Java 引擎。  为这些核心 NetWeaver 应用程序提供支持的是许多特定的小型非 NetWeaver SAP 独立引擎，通常是一些非 SAP 应用程序。  

需要列出在某个环境中运行的所有 SAP 应用程序，并确定部署模式（2 层或 3 层）、版本、修补程序、大小、改动率和磁盘持久性要求，这至关重要。

![部署模式](./media/site-recovery-sap/sap-typical-deployment.png)

SAP 数据库持久性层应通过本机 DBMS 工具（如 SQL Server AlwaysOn、Oracle DataGuard 或 HANA System Replication）进行保护。 客户端层也不由 Azure Site Recovery 进行保护，但务必要考虑影响此层的主题，如 DNS 传播延迟、安全性以及对灾难恢复数据中心的远程访问。

Azure Site Recovery 是针对应用层（包括 (A)SCS）的推荐解决方案。 其他应用程序（例如非 NetWeaver SAP 应用程序和非 SAP 应用程序）组成整体 SAP 部署环境的一部分，也应使用 Azure Site Recovery 进行保护。

## <a name="replicate-virtual-machines"></a>复制虚拟机
按照[此指南](azure-to-azure-walkthrough-enable-replication.md)开始将所有 SAP 应用程序虚拟机复制到 Azure 灾难恢复数据中心。

如果使用的是静态 IP，则可以在“计算和网络”设置的“网络接口卡”部分中指定希望虚拟机采用的 IP。

![目标 IP](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>创建恢复计划
使用恢复计划可将多层应用程序中各个层的故障转移排序，从而可以保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请遵循[此处](site-recovery-create-recovery-plans.md)介绍的步骤。

### <a name="adding-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移/测试故障转移后，可能需要在 Azure 虚拟机上执行一些操作才能让应用程序正常工作。 可以通过按[本文](site-recovery-create-recovery-plans.md#add-scripts)中所述在恢复计划中添加对应脚本，来自动执行故障转移后操作（如更新 DNS 条目以及更改绑定和连接）。

### <a name="dns-update"></a>DNS 更新
如果为动态 DNS 更新配置了 DNS，则虚拟机在启动后，通常会使用新的 IP 更新 DNS。 如果想要添加一个明确的步骤来使用虚拟机的新 IP 更新 DNS，请添加这个[用于更新 DNS 中的 IP 的脚本](https://aka.ms/asr-dns-update)，作为恢复计划组中的后期操作。  

## <a name="example-azure-to-azure-deployment"></a>Azure 到 Azure 部署示例
在下图中描绘了 Azure Site Recovery Azure 到 Azure 灾难恢复方案：
* 主数据中心位于新加坡（Azure 东南亚区域），灾难恢复数据中心位于香港（Azure 东亚区域）。  在此方案中，通过让两个 VM 在新加坡以同步模式运行 SQL Server AlwaysOn 来提供本地高可用性。
* 文件共享 ASCS 可以用于针对 SAP 单点故障提供高可用性。 文件共享 ASCS 不需要群集共享磁盘，并且不需要 SIOS 这类应用程序。
* 可以使用异步复制实现 DBMS 层的灾难恢复保护。
* 此方案显示了“对称灾难恢复”（一个术语，用于描述作为生产的精确副本的灾难恢复解决方案），因此灾难恢复 SQL Server 解决方案具有本地高可用性。 对于数据库层不强制使用对称灾难恢复，许多客户利用云部署的灵活性在灾难恢复事件之后快速构建本地高可用性节点。
* 该图描绘了由 Azure Site Recovery 复制的 SAP NetWeaver ASCS 和应用程序服务器层。

![复制方案](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>执行测试故障转移
遵循[此指南](azure-to-azure-walkthrough-test-failover.md)执行测试故障转移。

1.  转到 Azure 门户并选择恢复服务保管库。
2.  单击针对 SAP 应用程序创建的恢复计划。
3.  单击“测试故障转移”。
4.  选择恢复点和 Azure 虚拟网络开始测试故障转移过程。
5.  辅助环境启动后，可以执行验证。
6.  验证完成之后，单击“清理测试故障转移”以清除故障转移环境。

## <a name="doing-a-failover"></a>执行故障转移
执行故障转移时，请遵循[此指南](site-recovery-failover.md)。

1.  转到 Azure 门户并选择恢复服务保管库。
2.  单击针对 SAP 应用程序创建的恢复计划。
3.  单击“故障转移”。
4.  选择恢复点开始故障转移过程。

## <a name="next-steps"></a>后续步骤
在[本白皮书](http://aka.ms/asr-sap)中详细了解如何使用 Azure Site Recovery 为 SAP NetWeaver 部署构建灾难恢复解决方案。 本白皮书还讨论了针对不同 SAP 体系结构的建议，列出了 Azure 上的 SAP 所支持的应用程序和 VM 类型，并介绍了用于灾难恢复解决方案的可能测试计划。

详细了解如何使用 Site Recovery [复制其他工作负荷](site-recovery-workload.md)。
