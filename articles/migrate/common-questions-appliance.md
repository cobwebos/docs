---
title: Azure Migrate 设备常见问题
description: 获取有关 Azure Migrate 设备的常见问题的解答。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 3d0844b980ac418c5c334c2535c40dc5f3caeb16
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939283"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 设备：常见问题

本文解答了有关 Azure Migrate 设备的常见问题。 如果你有其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中获取问题答案

## <a name="what-is-the-azure-migrate-appliance"></a>什么是 Azure Migrate 设备？

Azure Migrate 设备是一个轻型设备，Azure Migrate 服务器评估工具使用该设备来发现和评估本地服务器。 Azure Migrate：服务器迁移工具还使用该设备进行本地 VMware Vm 的无代理迁移。

下面是有关 Azure Migrate 设备的详细信息：

- 设备在本地部署为 VM 或物理计算机。
- 设备会发现本地计算机，并持续将计算机元数据和性能数据发送到 Azure Migrate。
- 设备发现是无代理的。 已发现的计算机上未安装任何内容。

[了解](migrate-appliance.md)有关设备的详细信息。

## <a name="how-does-the-appliance-connect-to-azure"></a>设备如何连接到 Azure？

设备可以通过 internet 进行连接，或通过公共/Microsoft 对等互连使用 Azure ExpressRoute。

## <a name="does-appliance-analysis-affect-performance"></a>设备分析是否会影响性能？

Azure Migrate 设备会持续分析本地计算机上的性能数据。 此分析对分析的计算机的性能几乎没有影响。

## <a name="can-i-harden-the-appliance-vm"></a>能否强化设备 VM？

如果使用下载的模板来创建设备 VM，则可以将组件（例如，防病毒）添加到模板，前提是保留 Azure Migrate 设备所需的通信和防火墙规则。

## <a name="what-network-connectivity-is-required"></a>需要哪种网络连接？

有关 Azure Migrate 设备的网络连接要求的信息，请参阅以下文章：

- **VMware 评估**： [URL 访问](migrate-appliance.md#url-access)和[端口访问](migrate-support-matrix-vmware.md#port-access)
- **VMware 无代理迁移**： [URL 访问](migrate-appliance.md#url-access)和[端口访问](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Hyper-v 评估**： [URL 访问](migrate-appliance.md#url-access)和[端口访问](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

请参阅以下文章，了解 Azure Migrate 设备在 Vm 上收集的数据：

- **VMWARE VM**：[性能数据](migrate-appliance.md#collected-performance-data-vmware)和[元数据](migrate-appliance.md#collected-metadata-vmware)
- **HYPER-V VM**：[性能数据](migrate-appliance.md#collected-performance-data-hyper-v)和[元数据](migrate-appliance.md#collected-metadata-hyper-v)

## <a name="how-is-data-stored"></a>如何存储数据？

Azure Migrate 设备收集的数据存储在创建 Azure Migrate 项目的 Azure 位置。

下面是有关如何存储数据的详细信息：

- 收集的数据将安全地存储在 Microsoft 订阅中的 CosmosDB 中。 删除 Azure Migrate 项目时，数据将被删除。 存储由 Azure Migrate 处理。 不能为收集的数据专门选择存储帐户。
- 如果使用[依赖关系可视化](concepts-dependency-visualization.md)，收集的数据将存储在 azure 订阅中创建的 azure Log Analytics 工作区的美国中。 删除订阅中的 "Log Analytics" 工作区时，数据将被删除。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>连续分析期间上传了多少数据？

发送给 Azure Migrate 的数据量取决于多个参数。 例如，有10台计算机（每个计算机有一个磁盘，一个 NIC）的 Azure Migrate 项目每天发送大约 50 MB 的数据。 此值为近似值;实际值取决于磁盘和 Nic 的数据点数量。 如果计算机、磁盘或 Nic 数增加，则发送的数据的增加是非线性的。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>数据是否在静态和传输中加密？

是的，对于这两种情况：

- 元数据通过 HTTPS 安全地发送到 Azure Migrate 服务。
- 元数据存储在[Azure Cosmos](../cosmos-db/database-encryption-at-rest.md)数据库和 Microsoft 订阅中的[azure Blob 存储](../storage/common/storage-service-encryption.md)中。 为存储加密的元数据是静态的。
- 依赖关系分析的数据也会在传输过程中加密（通过安全 HTTPS）。 它存储在订阅的 Log Analytics 工作区中。 数据是静态加密的，以便进行依赖项分析。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>设备如何连接 vCenter Server？

以下步骤描述了设备如何连接到 VMware vCenter 服务器：

1. 设备使用设置设备时提供的凭据连接到 vCenter Server （端口443）。
2. 设备使用 VMware PowerCLI 来查询 vCenter Server 来收集 vCenter Server 管理的 Vm 的元数据。
3. 设备会收集有关过去个月内每个 VM 的 Vm （核心、内存、磁盘、Nic）和性能历史记录的配置数据。
4. 收集的元数据将发送到 Azure Migrate： Server 评估工具（通过 internet 通过 HTTPS）进行评估。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate 设备是否可以连接到多个 vCenter 服务器？

不是。 [Azure Migrate 设备](migrate-appliance.md)与 vCenter Server 之间存在一对一的映射。 若要发现多个 vCenter Server 实例上的 Vm，必须部署多个设备。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure Migrate 项目可以有多个设备吗？
一个项目可以附加多个设备。 但是，一个设备只能与一个项目关联。 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>可以通过设备发现多少 Vm 或服务器？

你最多可以发现10000个 VMware Vm （最多5000个 Hyper-v Vm），并且最多可使用单个设备来250物理服务器。 如果你在本地环境中有更多计算机，请阅读[扩展 hyper-v 评估](scale-hyper-v-assessment.md)、[扩展 VMware 评估](scale-vmware-assessment.md)和[缩放物理服务器评估](scale-physical-assessment.md)。

## <a name="can-i-delete-an-appliance"></a>能否删除设备？

当前不支持从项目中删除设备。

删除设备的唯一方法是删除包含与设备关联的 Azure Migrate 项目的资源组。

但是，删除资源组也会删除其他已注册的设备、发现的清单、评估以及与项目关联的资源组中的所有其他 Azure 组件。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>是否可以将设备用于其他订阅或项目？

使用设备启动发现后，不能将设备重新配置为与不同的 Azure 订阅一起使用，并且不能在不同 Azure Migrate 项目中使用它。 也无法在 vCenter Server 的不同实例上发现 Vm。 为这些任务设置新设备。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>能否在 Azure VM 上设置设备？

不是。 当前不支持此选项。 

## <a name="can-i-discover-on-an-esxi-host"></a>是否可以在 ESXi 主机上发现？

不是。 若要发现 VMware Vm，你必须拥有 vCenter Server。

## <a name="how-do-i-update-the-appliance"></a>如何实现更新设备？

默认情况下，设备及其安装的代理会自动更新。 设备每24小时检查一次更新。 重试失败的更新。 

这些自动更新仅更新设备和设备代理。 Azure Migrate 自动更新不会更新操作系统。 使用 Windows 更新来使操作系统保持最新状态。

## <a name="can-i-check-agent-health"></a>能否检查代理运行状况？

是的。 在门户中，前往 Azure Migrate： Server 评估或 Azure Migrate： Server 迁移工具的 "**代理运行状况**" 页。 可以在 Azure 与设备上的 "发现" 和 "评估" 代理之间检查连接状态。

## <a name="next-steps"></a>后续步骤

阅读[Azure Migrate 概述](migrate-services-overview.md)。
