---
title: Azure Migrate 设备的常见问题
description: 获取有关 Azure Migrate 设备的常见问题解答
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c8bcebeee58401cb3d2b65ae82e51d31ab4dad0b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029129"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 设备：常见问题

本文解答了有关 Azure Migrate 设备的常见问题。 阅读本文后，如果你有更多的查询，请将其发布到[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)。 如果你有其他问题，请查看以下文章：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)。
- 有关发现、评估和依赖项可视化的[问题](common-questions-discovery-assessment.md)。


## <a name="what-is-the-azure-migrate-appliance"></a>什么是 Azure Migrate 设备？

Azure Migrate 设备是一种轻型设备，由 Azure Migrate：服务器评估工具用于发现和评估本地服务器，并由 Azure Migrate：服务器迁移工具用于本地 VMware Vm 的无代理迁移。 

设备在本地部署为 VM 或物理计算机。 设备会发现本地计算机，并持续将计算机元数据和性能数据发送到 Azure Migrate。 设备发现是无代理的。 已发现的计算机上未安装任何内容。 [了解](migrate-appliance.md)有关设备的详细信息。

## <a name="how-does-the-appliance-connect-to-azure"></a>设备如何连接到 Azure？

可以通过 internet 连接，也可以将 Azure ExpressRoute 用于公共/Microsoft 对等互连。

## <a name="does-appliance-analysis-impact-performance"></a>设备分析是否会影响性能？

Azure Migrate 设备会连续分析本地计算机，以测量 VM 性能数据。 此分析对 Hyper-v/ESXi 主机或 VMware vCenter 服务器上的性能几乎没有影响。

### <a name="can-i-harden-the-appliance-vm"></a>能否强化设备 VM？

使用下载的模板创建设备 VM 时，可以将组件（例如防病毒软件）添加到模板，只要保留 Azure Migrate 设备所需的通信和防火墙规则。


## <a name="what-network-connectivity-is-needed"></a>需要哪些网络连接？

查看以下内容：
- 使用 Azure Migrate 设备的 VMware 评估： [URL](migrate-appliance.md#url-access)和[端口](migrate-support-matrix-vmware.md#port-access)访问要求。
- 使用 Azure Migrate 设备的 VMware 无代理迁移： [URL](migrate-appliance.md#url-access)和[端口](migrate-support-matrix-vmware-migration.md#agentless-ports)访问要求。
- 使用 Azure Migrate 设备进行 hyper-v 评估： [URL](migrate-appliance.md#url-access)和[端口](migrate-support-matrix-hyper-v.md#port-access)访问要求。


## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

查看收集的数据：

- VMware VM[性能数据](migrate-appliance.md#collected-performance-data-vmware)和[元数据](migrate-appliance.md#collected-metadata-vmware)。
- Hyper-v VM[性能数据](migrate-appliance.md#collected-performance-data-hyper-v)和[元数据](migrate-appliance.md#collected-metadata-hyper-v)。


## <a name="how-is-data-stored"></a>如何存储数据？

Azure Migrate 设备收集的数据存储在创建迁移项目时选择的 Azure 位置。 

- 数据将安全地存储在 Microsoft 订阅中，并在删除 Azure Migrate 项目时删除。
- 如果使用[依赖关系可视化](concepts-dependency-visualization.md)，收集的数据将存储在美国中，在 Azure 订阅中创建的 Log Analytics 工作区中。 在订阅中删除 Log Analytics 工作区时会删除此数据。

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>连续分析中上载了多少数据？

发送给 Azure Migrate 的数据量取决于多个参数。 为了让你了解卷，每个 Azure Migrate 包含10台计算机的项目（每个都有一个磁盘，一个 NIC）发送大约 50 MB。 此值为近似值，并根据 Nic 和磁盘的数据点数量进行了更改。 如果计算机、Nic 或磁盘的数量增加，则发送的数据量也是非线性的。

## <a name="is-data-encrypted-at-restin-transit"></a>数据是静态加密的还是传输中的？

是的。

- 元数据通过 HTTPS 安全地发送到 Azure Migrate 服务。
- 元数据存储在[Azure Cosmos](../cosmos-db/database-encryption-at-rest.md)数据库中，并存储在[azure Blob 存储](../storage/common/storage-service-encryption.md)中的 Microsoft 订阅中。 元数据是静态加密的。
- 依赖关系分析的数据也会在传输中加密（安全 HTTPS）。 它存储在订阅的 Log Analytics 工作区中。 它还静态加密。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>设备如何连接 vCenter Server？

1. 设备使用设置设备时提供的凭据连接到 vCenter Server （端口443）。
2. 设备使用 VMware PowerCLI 来查询 vCenter Server，以收集 vCenter Server 管理的 Vm 的元数据。
3. 设备会收集有关过去个月内每个 VM 的 Vm （核心、内存、磁盘、Nic）和性能历史记录的配置数据。
4. 收集的元数据 ent 到 Azure Migrate：服务器评估（通过 internet 通过 HTTPS）进行评估。

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>是否可以将设备连接到多个 vCenter 服务器？

不。 设备与 vCenter Server 之间存在一对一的映射。 若要发现多个 vCenter Server 实例上的 Vm，需要部署多个设备。

### <a name="how-many-vms-can-i-discover-with-an-appliance"></a>可以使用一个设备发现多少个 Vm？

使用单个设备，最多可以发现10000个 VMware Vm 和最多5000个 Hyper-v Vm。 如果你在本地环境中有更多计算机，请阅读有关扩展[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)评估的信息。

## <a name="can-i-delete-an-appliance"></a>能否删除设备？

当前不支持从项目中删除设备。

- 删除设备的唯一方法是删除包含与设备关联的 Azure Migrate 项目的资源组。
- 但是，删除资源组也会删除其他已注册的设备、发现的清单、评估以及与资源组中的项目相关联的所有其他 Azure 组件。


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>是否可以将设备用于其他订阅/项目？

使用设备启动发现后，不能使用其他 Azure 订阅或不同 Azure Migrate 项目重新配置它。 也无法在不同 vCenter Server 上发现 Vm。 为这些任务设置全新的设备。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>能否在 Azure VM 上设置设备？
当前不支持。 

## <a name="can-i-discover-on-an-esxi-host"></a>是否可以在 ESXi 主机上发现？
你不需要 vCenter Server 来发现 VMware Vm。

## <a name="how-do-i-update-the-appliance"></a>如何实现更新设备？

默认情况下，设备及其安装的代理会自动更新。 设备每24小时检查一次更新一次。 如果在更新过程中出现任何错误，则会出现重试过程。 自动更新仅更新设备和设备代理。 操作系统未更新。 使用 Microsoft 更新来使操作系统保持最新状态。

## <a name="can-i-check-agent-health"></a>能否检查代理运行状况？

在门户中，前往服务器评估或服务器迁移工具中的 "**代理运行状况**" 页。 在那里，可以在设备和 Azure 上的发现代理与评估代理之间检查连接状态。

## <a name="next-steps"></a>后续步骤
阅读[Azure Migrate 概述](migrate-services-overview.md)。
