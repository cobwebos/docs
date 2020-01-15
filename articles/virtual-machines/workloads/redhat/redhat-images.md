---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 211ac68fd10cd745faf68a5efae7392345008d7b
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941444"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映像的概述
本文介绍 Azure 市场中可用的 Red Hat Enterprise Linux (RHEL) 映像以及与其命名和保留相关的策略。

可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。 有关定价的详细信息，请访问[Azure 定价计算器](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> Azure 市场中当前提供的 RHEL 映像支持自带订阅 (BYOS) 或即用即付 (PAYG) 许可模型。 不支持 [Azure 混合使用权益](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)以及 BYOS 和 PAYG 之间的动态切换。 切换许可模式需要从相应的映像重新部署 VM。

>[!NOTE]
> 有关 Azure marketplace 中与 RHEL 映像相关的任何问题，请向 Microsoft 提交支持票证。

## <a name="images-available-in-azure"></a>Azure 中提供的映像
当你在 Marketplace 中搜索 "Red Hat" 时，或者在 Azure 门户 UI 中创建资源时，你将只看到所有可用的 RHEL 映像的子集。 只要使用 Azure CLI/PowerShell/API 就能获取完整的可用 VM 映像集。

若要在 Azure 中查看完整的可用 Red Hat 映像集，请运行以下命令

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名约定
Azure 中的 VM 映像按发布者、产品/服务、SKU 和版本进行组织。 发布者:产品/服务:SKU:版本的组合即为映像 URN，可唯一地标识要使用的映像。

例如，`RedHat:RHEL:7-LVM:7.6.2018103108` 是指在2018年10月31日构建的 RHEL 7.6 LVM 分区映像。

下面展示了有关如何创建 RHEL 7.6 VM 的示例。
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>“latest”名字对象
Azure REST API 允许为版本而不是特定版本使用名字对象 "最新"。 使用“latest”将为给定的发布者、产品/服务和 SKU 预配最新的可用映像。

例如，`RedHat:RHEL:7-LVM:latest` 是指可用的最新 RHEL 7 系列 LVM 分区映像。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> 一般情况下，通过比较版本来确定最新版本时应遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)的规则。

### <a name="rhel-6-image-types"></a>RHEL 6 映像类型
对于 RHEL 1.x 映像，图像类型如下：

|发布者 | 产品 | SKU 值 | 版本 | 详细信息
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次版本（例如6.9） | RHEL 次要版本和发布日期的串联值（例如6.9.2018010506） | 所有标准 RHEL 1.x 映像都遵循此约定
|RedHat | rhel-byos | rhel-raw69 | RHEL 次要版本和发布日期的串联值（例如6.9.20181023） | 此映像是 RHEL 6.9 BYOS 映像。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如6.8.2017053118） | 这是适用于 SAP 应用程序的 RHEL 6.8 映像。 它有权访问 SAP 应用程序存储库以及基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和发布日期的串联值（例如6.7.2017053121） | 这是 SAP HANA 映像的 RHEL 6.7。 它有权访问 SAP HANA 存储库以及基本 RHEL 存储库。

### <a name="rhel-7-image-types"></a>RHEL 7 映像类型
对于 RHEL 7、windows 映像，有几种不同的映像类型。 下表显示了我们提供的不同映像集。 可以使用 Az CLI 命令 `az vm image list --publisher redhat --all`查看完整列表。

>[!NOTE]
> 除非另有说明，否则所有映像均为 LVM 分区，并将连接到常规 RHEL 存储库（即，不是 EUS，而不是 E4S）。 今后，我们将仅发布 LVM 分区的映像，而是公开此决定的反馈。 [Red Hat Enterprise Linux 生命周期 "页面](https://access.redhat.com/support/policy/updates/errata)提供了有关适用于 SAP 的扩展更新支持和更新服务的详细信息。

|发布者 | 产品 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次版本（例如7.6） | RHEL 次要版本和发布日期的串联值（例如7.6.2019102813） | 2019年4月之前发布的映像将附加到标准 RHEL 存储库。 2019年4月发布的映像将附加到 Red Hat 的扩展更新支持（EUS）存储库，以允许锁定特定次版本的版本。 需要定期存储库的客户应在 SKU 值中使用包含 7-LVM 或 7-RAW 的映像（下面的详细信息）。 RHEL 7.7 和更高版本的映像将为 LVM 分区。 此类别中的所有其他映像均为原始分区。
|RedHat | RHEL | 7-RAW | RHEL 次要版本和发布日期的串联值（例如7.6.2019102813） | 这些映像是原始分区（即未添加逻辑卷）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本和发布日期的串联值（例如7.6.2019072418） | 这些映像是原始分区（即未添加逻辑卷），将使用 cloud init 进行预配。
|RedHat | RHEL | 7-LVM | RHEL 次要版本和发布日期的串联值（例如7.6.2019062414） | 这些映像为 LVM 分区。
|RedHat | rhel-byos | rhel-{lvm，原始} | RHEL 次要版本和发布日期的串联值（例如7.7.20190819） | 这些映像是 RHEL 7 BYOS 映像。 它们不会附加到任何存储库，也不会向 RHEL 高级版收费。 如果对 RHEL BYOS 映像感兴趣，请[请求访问权限](https://aka.ms/rhel-byos)。 SKU 值以次要版本结尾，表示映像是原始映像还是 LVM 分区映像。 例如，lvm77 SKU 值为 rhel-已分区的 RHEL 7.7 映像。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本和发布日期的串联值（例如7.6.2019071300） | 这些映像是 RHEL for SAP 映像。 它们有权访问 SAP HANA 和应用程序存储库以及 RHEL E4S 存储库。 计费包括 RHEL 高级版和基于基础计算费用的 SAP 高级版。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本和发布日期的串联值（例如7.6.2019062320） | 这些映像是 RHEL，适用于具有高可用性和更新服务映像的 SAP。 它们有权访问 SAP HANA 和应用程序存储库以及高可用性存储库以及 RHEL E4S 存储库。 在基础计算费用的基础上，计费包括 RHEL 高级版、SAP 高级版和 HA 高级版。
|RedHat | RHEL | RHEL-HA | RHEL 次要版本和发布日期的串联值（例如7.6.2019062019） | 这些是也有权访问高可用性附加项的 RHEL 映像。 由于 HA 附加项高级版，在 RHEL 和基础计算费用的基础上，它们将会稍微额外收费。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如7.3.2017053118） | 这些映像已过期，因为 SAP 应用程序和 SAP HANA 存储库已合并到 SAP 存储库中。 这些是 RHEL for SAP 应用程序映像。 它们有权访问 SAP 应用程序存储库以及基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和发布日期的串联值（例如7.3.2018051421） | 这些映像已过期，因为 SAP 应用程序和 SAP HANA 存储库已合并到 SAP 存储库中。 这些是 RHEL for SAP HANA 映像。 它们有权访问 SAP HANA 存储库以及基本 RHEL 存储库。

### <a name="rhel-8-image-types"></a>RHEL 8 映像类型
下面是 RHEL 8 映像类型的详细信息。

|发布者 | 产品 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本和发布日期的串联值（例如8.0.20191023） | 这些映像是已连接到标准 Red Hat 存储库的 RHEL 8.0 LVM 分区映像。
|RedHat | RHEL | 8-gen2 | RHEL 次要版本和发布日期的串联值（例如8.0.20191024） | 这些映像是连接到标准 Red Hat 存储库的 Hyper-v 第 2 RHEL 8.0 LVM 分区映像。 [此处](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)提供了有关 Azure 中第2代 vm 的详细信息。

## <a name="extended-update-support-eus"></a>扩展更新支持（EUS）
从2019年4月起，默认情况下，RHEL 映像可用于附加到扩展更新支持（EUS）存储库。 有关 RHEL EUS 的更多详细信息，请[参阅 Red Hat 的文档](https://access.redhat.com/articles/rhel-eus)。

可以切换到 EUS 存储库，并且支持。 [此处](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)提供了有关如何将 VM 切换到 EUS 的说明，以及有关 EUS 支持的更多详细信息。

>[!NOTE]
> RHEL 附加上不支持 EUS。 这意味着，如果你要安装的包通常在 RHEL 额外通道中可用，则在 EUS 上将无法执行此操作。 [此处](https://access.redhat.com/support/policy/updates/extras/)详细介绍了 Red Hat 额外产品生命周期。

### <a name="differentiating-between-regular-and-eus-images"></a>区分 regular 和 EUS 映像。
如果客户想要使用附加到 EUS 存储库的映像，则应在 SKU 中使用包含 RHEL 次版本号的 RHEL 映像。

例如，你可能会看到以下两个 RHEL 7.4 映像可用：
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
在这种情况下，默认情况下 `RedHat:RHEL:7.6:7.6.2019102813` 会附加到 EUS 存储库（SKU 值为7.4），默认情况下 `RedHat:RHEL:7-LVM:7.6.2019062414` 将附加到非 EUS 存储库（SKU 值为 7-LVM）。

如果要使用常规（非 EUS）存储库，请使用 SKU 中不包含次版本号的映像进行部署。

#### <a name="rhel-images-with-eus"></a>具有 EUS 的 RHEL 映像
下表适用于连接到 EUS 存储库的 RHEL 映像。

>[!NOTE]
> 撰写本文时，只有 RHEL 7.4 和更高版本的 EUS 支持。 RHEL < 不再支持 EUS = 7.3。
>
> 有关 RHEL EUS 可用性的详细信息，请参阅[此处](https://access.redhat.com/support/policy/updates/errata)。

次版本 |EUS 图像示例              |EUS 状态                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7。4      |RedHat： RHEL：7.4：7.4.2019041718 | 默认情况下，将 EUS 2019 年4月版和更高版本发布的映像|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 默认情况下，将 EUS 2019 年6月发布的映像 |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 默认情况下，发布的映像可能为2019，以后将 EUS  |
RHEL 8。0      |N/A                            | Red Hat 中没有可用的 EUS                               |





### <a name="other-available-offers-and-skus"></a>其他可用产品/服务和 SKU
可用产品/服务和 SKU 的完整列表可能还包括除上表所列映像以外的其他映像，例如 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 这些产品/服务可用于为特定市场解决方案提供支持，或者可以发布用于预览和测试目的。 它们可能随时被更改或删除，而不会发出警告。 除非 Microsoft 或 Red Hat 已将它们公开记录在案，否则请勿使用它们。

## <a name="publishing-policy"></a>发布策略
Microsoft 和 Red Hat 会在以下情况下更新映像：新的次要版本发布时，根据需要解决特定的 CVE 时，或者偶尔进行配置更改/更新时。 我们会尽快提供更新的映像 - 在发布或提供 CVE 修补程序后的三个工作日内。

我们只更新给定映像系列中的当前次要版本。 随着较新次要版本的发布，我们将停止更新较旧的次要版本。 例如，随着 RHEL 7.6 的发布，RHEL 7.5 映像将不再更新。

>[!NOTE]
> 从 RHEL 即用即付映像预配的活动 Azure VM 会连接到 Azure RHUI，并且可以在 Red Hat 发布更新和修补程序并复制到 Azure RHUI 后便立即收到这些更新和修补程序（通常在 Red Hat 正式发布后的 24 小时内）。 这些 VM 不需要通过发布的新映像来获取更新，客户可以完全控制何时启动更新。

## <a name="image-retention-policy"></a>映像保留策略
我们目前的策略是保留以前发布的所有映像。 我们保留删除已知会导致任何问题的映像的权利。 例如，可能删除由于后续平台或组件更新而具有不正确配置的映像。 可能被删除的映像将遵循当前的市场策略，在映像删除前的 30 天内提供通知。

## <a name="next-steps"></a>后续步骤
* 查看[Azure 中 RHEL 映像](./redhat-imagelist.md)的完整列表。
* 从[此处](https://aka.ms/rhui-update)了解有关 Azure Red Hat 更新基础结构的详细信息。
* 了解有关[RHEL BYOS 产品/服务](./byos.md)的详细信息。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
