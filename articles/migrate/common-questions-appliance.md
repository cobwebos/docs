---
title: Azure Migrate 设备常见问题
description: 获取有关 Azure Migrate 设备的常见问题的解答。
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: de34bba40b9200c198f3c07262bd6b7a00b62060
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050669"
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
- 设备发现是无代理的。 无需在已发现的计算机上安装任何组件。

[了解](migrate-appliance.md) 有关设备的详细信息。

## <a name="how-can-i-deploy-the-appliance"></a>如何部署设备？

可按如下所示部署设备：

- 使用适用于 VMware Vm 和 Hyper-v Vm 的模板 (适用于 Hyper-v) 的 VMware 或 VHD 的 .OVA 模板。
- 如果不想使用模板，或者在 Azure 政府版中，则可以使用 PowerShell 脚本部署适用于 VMware 或 Hyper-v 的设备。
- 对于物理服务器，应始终使用脚本部署设备。

## <a name="how-does-the-appliance-connect-to-azure"></a>设备如何连接到 Azure？

设备可以通过 internet 或使用 Azure ExpressRoute 进行连接。

- 若要将 Azure ExpressRoute 用于 Azure Migrate 复制流量，需要 Microsoft 对等互连或现有的公共对等互连， (对新的 ER 创建) 弃用了公共对等互连。
- 不支持通过 Azure ExpressRoute 复制，只启用了) 专用对等互连 (。

配置了 Microsoft 对等互连和 Microsoft 对等互连的 Azure ExpressRoute。

## <a name="does-appliance-analysis-affect-performance"></a>设备分析是否会影响性能？

Azure Migrate 设备会持续分析本地计算机上的性能数据。 此分析对分析的计算机的性能几乎没有影响。

## <a name="can-i-harden-the-appliance-vm"></a>能否强化设备 VM？

如果使用下载的模板来创建设备 VM，则可以将组件添加 (防病毒，例如) 到模板（如果保留了 Azure Migrate 设备所需的通信和防火墙规则）。

## <a name="what-network-connectivity-is-required"></a>需要哪种网络连接？

设备需要访问 Azure Url。 [查看](migrate-appliance.md#url-access) URL 列表。

## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

请参阅以下文章，了解 Azure Migrate 设备在 Vm 上收集的数据：

- **VMWARE VM**： [查看](migrate-appliance.md#collected-data---vmware) 收集的数据。
- **HYPER-V VM**： [查看](migrate-appliance.md#collected-data---hyper-v) 收集的数据。

## <a name="how-is-data-stored"></a>如何存储数据？

Azure Migrate 设备收集的数据存储在创建 Azure Migrate 项目的 Azure 位置。

下面是有关如何存储数据的详细信息：

- 收集的数据将安全地存储在 Microsoft 订阅中的 CosmosDB 中。 删除 Azure Migrate 项目时，数据将被删除。 存储由 Azure Migrate 处理。 不能为收集的数据专门选择存储帐户。
- 如果使用 [依赖关系可视化](concepts-dependency-visualization.md)，收集的数据将存储在 azure 订阅中创建的 azure Log Analytics 工作区的美国中。 删除订阅中的 "Log Analytics" 工作区时，数据将被删除。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>连续分析期间上传了多少数据？

发送给 Azure Migrate 的数据量取决于多个参数。 例如，具有10台计算机的 Azure Migrate 项目 (每个计算机有一个磁盘，一个 NIC) 每天发送大约 50 MB 的数据。 此值为近似值;实际值取决于磁盘和 Nic 的数据点数量。 如果计算机、磁盘或 Nic 数增加，则发送的数据的增加是非线性的。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>数据是否在静态和传输中加密？

是的，对于这两种情况：

- 元数据通过 HTTPS 安全地发送到 Azure Migrate 服务。
- 元数据存储在 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 数据库和 Microsoft 订阅中的 [azure Blob 存储](../storage/common/storage-service-encryption.md) 中。 为存储加密的元数据是静态的。
- 用于依赖项分析的数据也通过安全 HTTPS)  (传输中进行加密。 它存储在订阅的 Log Analytics 工作区中。 数据是静态加密的，以便进行依赖项分析。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>设备如何连接 vCenter Server？

以下步骤描述了设备如何连接到 VMware vCenter 服务器：

1. 设备使用设置设备时提供的凭据连接到 vCenter Server (端口 443) 。
2. 设备使用 VMware PowerCLI 来查询 vCenter Server 来收集 vCenter Server 管理的 Vm 的元数据。
3. 设备会收集有关 Vm 的配置数据 (核心、内存、磁盘、Nic) ，以及上个月每个 VM 的性能历史记录。
4. 收集的元数据将通过通过 internet (Azure Migrate 服务器评估工具发送到 internet，) 进行评估。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate 设备是否可以连接到多个 vCenter 服务器？

不是。 [Azure Migrate 设备](migrate-appliance.md)与 vCenter Server 之间存在一对一的映射。 若要发现多个 vCenter Server 实例上的 Vm，必须部署多个设备。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure Migrate 项目可以有多个设备吗？

一个项目可以附加多个设备。 但是，一个设备只能与一个项目关联。 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate 设备/复制设备是否可以连接到同一 vCenter？

是的。 你可以同时添加用于评估和无代理 VMware 迁移) 的 Azure Migrate (设备，并将复制设备 (用于 VMware Vm 的基于代理的迁移) 到相同的 vCenter 服务器。


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>可以通过设备发现多少 Vm 或服务器？

你最多可以发现10000个 VMware Vm （最多5000个 Hyper-v Vm），并且最多可使用单个设备来1000物理服务器。 如果你在本地环境中有更多计算机，请阅读 [扩展 hyper-v 评估](scale-hyper-v-assessment.md)、 [扩展 VMware 评估](scale-vmware-assessment.md)和 [缩放物理服务器评估](scale-physical-assessment.md)。

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

是的。 在门户中，前往 Azure Migrate： Server 评估或 Azure Migrate： Server 迁移工具的 " **代理运行状况** " 页。 可以在 Azure 与设备上的 "发现" 和 "评估" 代理之间检查连接状态。

## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。
