---
title: "使用 Azure Site Recovery 保护多层 SAP NetWeaver 应用程序部署 | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 保护 SAP NetWeaver 应用程序部署。"
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
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>使用 Site Recovery 保护多层 SAP NetWeaver 应用程序部署

大多数大型和中型 SAP 部署具有某种形式的灾难恢复解决方案。 随着更多核心业务流程迁移到 SAP 等应用程序中，可靠和可测试的灾难恢复解决方案的重要性也越来越高。 Azure Site Recovery 已经过测试并与 SAP 应用程序集成。 Site Recovery 超过大多数本地灾难恢复解决方案的功能，并且总拥有成本 (TCO) 要低于竞争对手解决方案。

使用 Site Recovery 可以：
* 将组件复制到 Azure，以便**保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序**。
* 将组件复制到其他 Azure 数据中心，以便**保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序**。
* 使用 Site Recovery 将 SAP 部署迁移到 Azure，从而**简化云迁移**。
* 通过创建一个按需生产克隆来测试 SAP 应用程序，**简化 SAP 项目的升级、测试和原型制作**。

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 保护 SAP NetWeaver 应用程序部署。 本文介绍通过使用 Site Recovery 复制到其他 Azure 数据中心来保护 Azure 上的三层 SAP NetWeaver 部署的最佳做法。 此外，介绍支持的方案和配置，以及如何执行测试故障转移（灾难恢复演练）和实际故障转移。

## <a name="prerequisites"></a>先决条件
在开始之前，请确保知道如何执行以下任务：

* [将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md)
* [设计恢复网络](site-recovery-azure-to-azure-networking-guidance.md)
* [执行到 Azure 的测试故障转移](azure-to-azure-walkthrough-test-failover.md)
* [执行到 Azure 的故障转移](site-recovery-failover.md)
* [复制域控制器](site-recovery-active-directory.md)
* [复制 SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支持的方案
使用 Azure Site Recovery 可以为以下方案实现灾难恢复解决方案：
* 在一个 Azure 数据中心内运行的 SAP 系统复制到其他 Azure 数据中心（Azure 到 Azure 灾难恢复）。 有关详细信息，请参阅 [Azure 到 Azure 复制体系结构](https://aka.ms/asr-a2a-architecture)。
* 在本地 VMware（或物理）服务器上运行的 SAP 系统复制到 Azure 数据中心内的灾难恢复站点（VMware 到 Azure 灾难恢复）。 此方案需要一些附加的组件。 有关详细信息，请参阅 [VMware 到 Azure 复制体系结构](https://aka.ms/asr-v2a-architecture)。
* 在本地 Hyper-V 上运行的 SAP 系统复制到 Azure 数据中心内的灾难恢复站点（Hyper-V 到 Azure 灾难恢复）。 此方案需要一些附加的组件。 有关详细信息，请参阅 [Hyper-V 到 Azure 复制体系结构](https://aka.ms/asr-h2a-architecture)。

本文使用 Azure 到 Azure 灾难恢复方案演示 Site Recovery 的 SAP 灾难恢复功能。 由于 Site Recovery 复制过程不特定于应用程序，因此介绍的过程应该也适用于其他方案。

### <a name="required-foundation-services"></a>所需基础服务
在本文所述的方案中，已部署以下基础服务：
* Azure ExpressRoute 或 Azure VPN 网关
* 至少有一个在 Azure 中运行的 Active Directory 域控制器和 DNS 服务器

建议在部署 Site Recovery 之前建立此基础结构。

## <a name="typical-sap-application-deployment"></a>典型 SAP 应用程序部署
大型 SAP 客户通常部署 6 到 20 个不同的 SAP 应用程序。 这些应用程序中的大多数基于 SAP NetWeaver ABAP 或 Java 引擎。 许多特定的小型非 NetWeaver SAP 独立引擎（通常也是一些非 SAP 应用程序）都支持这些核心 NetWeaver 应用程序。  

必须盘点环境中运行的所有 SAP 应用程序。 然后，确定部署模式（两层或三层）、版本、修补程序、大小、改动率和磁盘持久性要求。

![典型 SAP 部署模式的示意图](./media/site-recovery-sap/sap-typical-deployment.png)

SAP 数据库持久性层应通过本机 DBMS 工具（如 SQL Server AlwaysOn、Oracle Data Guard 或 SAP HANA 系统复制）进行保护。 与 SAP 数据库层一样，客户端层也不受 Site Recovery 的保护。 必须考虑到影响此层的因素。 因素包括 DNS 传播延迟、安全性以及对灾难恢复数据中心的远程访问。

Site Recovery 是用于应用层（包括 SAP SCS 和 ASCS）的建议解决方案。 其他应用程序（例如非 NetWeaver SAP 应用程序和非 SAP 应用程序）组成整体 SAP 部署环境的一部分。 应使用 Site Recovery 保护这些应用程序。

## <a name="replicate-virtual-machines"></a>复制虚拟机
若要开始将所有 SAP 应用程序虚拟机复制到 Azure 灾难恢复数据中心，请遵照[将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md) 中的指导。

如果使用的是静态 IP，可以指定希望虚拟机采用的 IP 地址。 若要设置 IP 地址，请转到“计算和网络设置” > “网络接口卡”。

![演示如何在 Site Recovery 的“网络接口卡”窗格中设置专用 IP 地址的屏幕截图](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>创建恢复计划
恢复计划支持在故障转移期间将多层应用程序中的各个层排序。 排序有助于保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请完成[使用 Site Recovery 创建恢复计划](site-recovery-create-recovery-plans.md)中所述的步骤。

### <a name="add-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移后或测试故障转移期间，可能需要在 Azure 虚拟机上执行一些操作才能让应用程序正常工作。 可将某些故障转移后的操作自动化。 例如，可在恢复计划中添加相应的脚本，来更新 DNS 条目，以及更改绑定和连接。

### <a name="dns-update"></a>DNS 更新
如果为动态 DNS 更新配置了 DNS，则虚拟机在启动时，通常会使用新的 IP 地址更新 DNS。 如果想要添加一个明确的步骤来使用虚拟机的新 IP 地址更新 DNS，请添加这个[用于更新 DNS 中的 IP 地址的脚本](https://aka.ms/asr-dns-update)，作为恢复计划组中的故障转移后操作。  

## <a name="example-azure-to-azure-deployment"></a>Azure 到 Azure 部署示例
下图演示了 Site Recovery Azure 到 Azure 灾难恢复方案：

![Azure 到 Azure 复制方案示意图](./media/site-recovery-sap/sap-replication-scenario.png)

* 主要数据中心位于新加坡（Azure 东南亚区域）。 灾难恢复数据中心位于香港（Azure 东亚区域）。 在此方案中，通过让两个 VM 在新加坡以同步模式运行 SQL Server AlwaysOn 来提供本地高可用性。
* 文件共享 SAP ASCS 为 SAP 单一故障点提供高可用性。 文件共享 ASCS 不需要群集共享磁盘。 不需要 SIOS 之类应用程序。
* 可以使用异步复制实现 DBMS 层的灾难恢复保护。
* 此方案显示了“对称灾难恢复”。 此术语描述采用确切生产副本的灾难恢复解决方案。 灾难恢复 SQL Server 解决方案具有本地高可用性。 对于数据库层不强制使用对称灾难恢复。 许多客户利用云部署的灵活性在灾难恢复事件之后快速构建本地高可用性节点。
* 该图描绘了由 Site Recovery 复制的 SAP NetWeaver ASCS 和应用程序服务器层。

## <a name="run-a-test-failover"></a>运行测试故障转移

1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择针对 SAP 应用程序创建的恢复计划。
3.  选择“测试故障转移”。
4.  若要启动测试故障转移过程，请选择恢复点和 Azure 虚拟网络。
5.  当辅助环境启动时，可以执行验证。
6.  完成验证后，选择“清理测试故障转移”可清理测试故障转移环境。

有关详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>运行故障转移

1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择针对 SAP 应用程序创建的恢复计划。
3.  选择“故障转移”。
4.  若要启动故障转移过程，请选择恢复点。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何使用 Site Recovery 为 SAP NetWeaver 部署构建灾难恢复解决方案，请参阅可下载的白皮书 [SAP NetWeaver：使用 Azure Site Recovery 构建灾难恢复解决方案](http://aka.ms/asr-sap)。 本白皮书介绍了针对各种 SAP 体系结构的建议，列出了 Azure 上的 SAP 所支持的应用程序和 VM 类型，并介绍了用于灾难恢复解决方案的测试计划选项。
* 详细了解如何使用 Site Recovery [复制其他工作负荷](site-recovery-workload.md)。
