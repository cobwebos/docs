---
title: Azure 迁移设备常见问题解答
description: 获取有关 Azure 迁移设备的常见问题的解答。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 529ead1baa769ee3e71f6fcf77ef7e020ed196a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529682"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure 迁移设备：常见问题

本文回答了有关 Azure 迁移设备的常见问题。 如果您有其他问题，请检查以下资源：

- 有关 Azure 迁移的[一般问题](resources-faq.md)
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在[Azure 迁移论坛](https://aka.ms/AzureMigrateForum)中回答问题

## <a name="what-is-the-azure-migrate-appliance"></a>什么是 Azure 迁移设备？

Azure 迁移设备是 Azure 迁移：服务器评估工具用于发现和评估本地服务器的轻量级设备。 Azure 迁移：服务器迁移工具还使用该设备对本地 VMware VM 进行无代理迁移。

以下是有关 Azure 迁移设备的详细信息：

- 设备在本地部署为 VM 或物理计算机。
- 设备发现本地计算机，并不断向 Azure 迁移发送计算机元数据和性能数据。
- 设备发现是无代理的。 未在已发现的计算机上安装任何内容。

[了解有关产品详细信息](migrate-appliance.md)。

## <a name="how-can-i-deploy-the-appliance"></a>如何部署设备？

设备可以部署如下：

- 使用 VMware VM 和超 V VM 的模板（用于 VMware 的 OVA 模板或用于 Hyper-V 的 VHD）。
- 如果不想使用模板，或者您位于 Azure 政府中，则可以使用 PowerShell 脚本部署适用于 VMware 或 Hyper-V 的设备。
- 对于物理服务器，您始终使用脚本部署设备。


## <a name="how-does-the-appliance-connect-to-azure"></a>设备如何连接到 Azure？

设备可以通过 Internet 或将 Azure ExpressRoute 与公共/微软对等互连连接。

## <a name="does-appliance-analysis-affect-performance"></a>设备分析会影响性能吗？

Azure 连续迁移本地计算机的设备配置文件，以测量性能数据。 此分析对已分析的计算机几乎没有性能影响。

## <a name="can-i-harden-the-appliance-vm"></a>我可以强化设备 VM 吗？

使用下载的模板创建设备 VM 时，如果保留 Azure 迁移设备所需的通信和防火墙规则，则可以向模板添加组件（例如防病毒）。

## <a name="what-network-connectivity-is-required"></a>需要哪些网络连接？


设备需要访问 Azure URL。 [查看](migrate-appliance.md#url-access)URL 列表。

## <a name="what-data-does-the-appliance-collect"></a>产品收集哪些数据？

有关 Azure 迁移设备在 VM 上收集的数据的信息，请参阅以下文章：

- **VMware VM**：[查看](migrate-appliance.md#collected-data---vmware)收集的数据。 [
- **Hyper-V VM**：[查看](migrate-appliance.md#collected-data---hyper-v)收集的数据。

## <a name="how-is-data-stored"></a>如何存储数据？

Azure 迁移设备收集的数据存储在创建 Azure 迁移项目的 Azure 位置。

以下是有关如何存储数据的详细信息：

- 收集的数据安全地存储在 Microsoft 订阅中。 删除 Azure 迁移项目时，将删除数据。 存储由 Azure 迁移处理。 不能为收集的数据专门选择存储帐户。
- 如果使用[依赖项可视化](concepts-dependency-visualization.md)，则收集的数据将存储在 Azure 订阅中创建的 Azure 日志分析工作区中。 删除订阅中的日志分析工作区时，数据将被删除。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>在连续分析期间上传了多少数据？

发送到 Azure 迁移的数据量取决于多个参数。 例如，具有 10 台计算机（每个计算机有一个磁盘和一个 NIC）的 Azure 迁移项目每天发送大约 50 MB 的数据。 此值是近似值;实际值因磁盘和 NIC 的数据点数而异。 如果计算机、磁盘或 NIC 的数量增加，则发送的数据的增加是非线性的。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>数据是否在静态和传输中加密？

是，对于这两个：

- 元数据通过 HTTPS 通过互联网安全地发送到 Azure 迁移服务。
- 元数据存储在 Azure[宇宙](../cosmos-db/database-encryption-at-rest.md)数据库中，存储在 Microsoft 订阅中的[Azure Blob 存储](../storage/common/storage-service-encryption.md)中。 元数据在静态时进行加密以进行存储。
- 依赖项分析的数据也在传输过程中（通过安全 HTTPS）进行加密。 它存储在订阅中的日志分析工作区中。 数据在静态时进行加密，以便依赖项分析。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>设备如何连接到 vCenter 服务器？

这些步骤描述了设备如何连接到 VMware vCenter 服务器：

1. 设备使用设置设备时提供的凭据连接到 vCenter 服务器（端口 443）。
2. 设备使用 VMware PowerCLI 查询 vCenter 服务器来收集有关 vCenter 服务器管理的 VM 的元数据。
3. 设备收集有关 VM（内核、内存、磁盘、NIC）和每个 VM 过去一个月的性能历史记录的配置数据。
4. 收集的元数据将发送到 Azure 迁移：服务器评估工具（通过互联网通过 HTTPS 进行评估）。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure 迁移设备是否可以连接到多个 vCenter 服务器？

不是。 [Azure 迁移设备和](migrate-appliance.md)vCenter 服务器之间存在一对一映射。 要发现多个 vCenter Server 实例上的 VM，必须部署多个设备。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure 迁移项目是否可以具有多个设备？
项目可以附加多个设备。 但是，设备只能与一个项目关联。 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure 迁移设备/复制设备是否可以连接到同一个 vCenter？
是的。 您可以将 Azure 迁移设备（用于评估和无代理 VMware 迁移）和复制设备（用于基于代理的 VMware VM 迁移）添加到同一 vCenter 服务器。


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>使用设备可以发现多少个 VM 或服务器？

您最多可以发现 10，000 个 VMware VM、多达 5，000 台超虚拟机，以及最多 250 台物理服务器，只需一台设备。 如果本地环境中有更多的计算机，请阅读有关[缩放 Hyper-V 评估](scale-hyper-v-assessment.md)、[缩放 VMware 评估](scale-vmware-assessment.md)以及[缩放物理服务器评估](scale-physical-assessment.md)。

## <a name="can-i-delete-an-appliance"></a>我可以删除设备吗？

目前，不支持从项目中删除设备。

删除设备的唯一方法是删除包含与设备关联的 Azure 迁移项目的资源组。

但是，删除资源组还会删除与项目关联的其他已注册设备、已发现的清单、评估以及资源组中的所有其他 Azure 组件。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>我可以将设备用于不同的订阅或项目吗？

使用设备启动发现后，无法将设备重新配置为与其他 Azure 订阅一起使用，也不能在其他 Azure 迁移项目中使用它。 您也不能在 vCenter Server 的不同实例上发现 VM。 为这些任务设置新设备。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>是否可以在 Azure VM 上设置设备？

不是。 目前，此选项不受支持。 

## <a name="can-i-discover-on-an-esxi-host"></a>我可以在 ESXi 主机上发现吗？

不是。 要发现 VMware VM，您必须具有 vCenter 服务器。

## <a name="how-do-i-update-the-appliance"></a>如何更新产品？

默认情况下，设备及其安装的代理会自动更新。 设备每 24 小时检查一次更新。 将重试失败的更新。 

这些自动更新仅更新设备和产品代理。 Azure 迁移自动更新不会更新操作系统。 使用 Windows 更新使操作系统保持最新。

## <a name="can-i-check-agent-health"></a>我可以检查代理运行状况吗？

是的。 在门户中，转到 Azure 迁移的**代理运行状况**页：服务器评估或 Azure 迁移：服务器迁移工具。 在那里，可以检查 Azure 与设备上的发现和评估代理之间的连接状态。

## <a name="next-steps"></a>后续步骤

阅读[Azure 迁移概述](migrate-services-overview.md)。
