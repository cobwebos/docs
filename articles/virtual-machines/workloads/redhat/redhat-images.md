---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 7913cb888e1799efae0f3ecdf3391d19736cc273
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970148"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映像的概述

本文介绍 Azure Marketplace 中的可用 Red Hat Enterprise Linux （RHEL）映像，并围绕其命名和保留策略。

有关适用于所有版本 RHEL 的 Red Hat 支持策略的信息，请参阅[Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。 有关定价的详细信息，请参阅[Azure 定价计算器](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> 当前在 Azure Marketplace 中提供的 RHEL 映像支持自带订阅（BYOS）或即用即付许可模式。 不支持在 BYOS 与即用即付许可之间进行[Azure 混合使用权益](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)和动态切换。 若要切换授权模式，你必须从相应的映像重新部署 VM。

>[!NOTE]
> 对于 Azure Marketplace 中的 RHEL 映像相关的任何问题，请向 Microsoft 提交支持票证。

## <a name="view-images-available-in-azure"></a>查看 Azure 中提供的映像

当你在 Azure Marketplace 中搜索 "Red Hat" 时，或者在 Azure 门户 UI 中创建资源时，你将只看到所有可用的 RHEL 映像的子集。 你始终可以使用 Azure CLI、PowerShell 和 API 获取可用 VM 映像的完整集。

若要在 Azure 中查看完整的可用 Red Hat 映像集，请运行以下命令：

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名约定

Azure 中的 VM 映像按发布者、产品/服务、SKU 和版本进行组织。 发布者:产品/服务:SKU:版本的组合即为映像 URN，可唯一地标识要使用的映像。

例如，`RedHat:RHEL:7-LVM:7.6.2018103108` 是指在2018年10月31日构建的 RHEL 7.6 LVM 分区映像。

此处显示了如何创建 RHEL 7.6 VM 的示例。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>“latest”名字对象

Azure REST API 允许为版本而不是特定版本使用名字对象 "最新"。 使用 "最新" 可为给定的发布者、产品/服务和 SKU 预配最新的可用映像。

例如，`RedHat:RHEL:7-LVM:latest` 是指可用的最新 RHEL 7 系列 LVM 分区映像。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> 一般情况下，通过比较版本来确定最新版本时应遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)的规则。
此映像版本比较是通过将值作为[版本](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8)对象而不是字符串进行比较来完成的。

## <a name="rhel-6-image-types"></a>RHEL 6 映像类型

对于 RHEL 1.x 映像，下表显示了映像类型。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次版本（例如，6.9） | RHEL 次要版本和发布日期的串联值（例如，6.9.2018010506） | 所有标准 RHEL 1.x 映像都遵循此约定。
|RedHat | rhel-byos | rhel-raw69 | RHEL 次要版本和发布日期的串联值（例如，6.9.20181023） | 此映像是 RHEL 6.9 BYOS 映像。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如，6.8.2017053118） | 此映像是适用于 SAP 应用程序的 RHEL 6.8 映像。 它有权访问 SAP 应用程序存储库和基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和发布日期的串联值（例如，6.7.2017053121） | 此映像是 SAP HANA 映像的 RHEL 6.7。 有权访问 SAP HANA 存储库和基本 RHEL 存储库。

## <a name="rhel-7-image-types"></a>RHEL 7 映像类型

对于 RHEL 7、windows 映像，有几种不同的映像类型。 下表显示了我们提供的不同映像集。 若要查看完整列表，请使用 Azure CLI 命令 `az vm image list --publisher redhat --all`。

>[!NOTE]
> 除非另有说明，否则，将对所有映像进行 LVM 分区，并连接到常规 RHEL 存储库。 也就是说，存储库不扩展更新支持（EUS），也不更新 SAP （E4S）服务。 今后，我们将转到仅发布 LVM 分区的图像，但打开此决定的反馈。 有关 SAP 的扩展更新支持和更新服务的详细信息，请参阅[Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次版本（例如，7.6） | RHEL 次要版本和发布日期的串联值（例如，7.6.2019102813） | 2019年4月之前发布的映像附加到标准 RHEL 存储库。 2019年4月之后发布的映像会附加到 Red Hat 的 EUS 存储库，以允许锁定特定次要版本。 需要常规存储库的客户应使用 SKU 值中包含 7-LVM 或 7-RAW 的映像（详细信息如下）。 RHEL 7.7 和更高版本的映像已分区。 此类别中的所有其他映像都是原始分区。
|RedHat | RHEL | 7-RAW | RHEL 次要版本和发布日期的串联值（例如，7.6.2019102813） | 这些映像是原始分区（例如，未添加逻辑卷）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本和发布日期的串联值（例如，7.6.2019072418） | 这些映像是原始分区（例如，未添加任何逻辑卷），并使用 cloud init 进行设置。
|RedHat | RHEL | 7-LVM | RHEL 次要版本和发布日期的串联值（例如，7.6.2019062414） | 这些映像为 LVM 分区。
|RedHat | rhel-byos | rhel-{lvm，原始} | RHEL 次要版本和发布日期的串联值（例如，7.7.20190819） | 这些映像是 RHEL 7 BYOS 映像。 它们不会附加到任何存储库，也不会向 RHEL 高级版收费。 如果你对 RHEL BYOS 映像感兴趣，请[请求访问权限](https://aka.ms/rhel-byos)。 SKU 值以次要版本结尾，并指示映像是原始分区还是 LVM 分区。 例如，lvm77 SKU 值为 rhel-已分区的 RHEL 7.7 映像。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本和发布日期的串联值（例如，7.6.2019071300） | 这些映像是 RHEL for SAP 映像。 他们有权访问 SAP HANA 和应用程序存储库以及 RHEL E4S 存储库。 计费包括 RHEL 高级版和基于基础计算费用的 SAP 高级版。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本和发布日期的串联值（例如，7.6.2019062320） | 这些映像是 RHEL，适用于具有高可用性和更新服务映像的 SAP。 他们有权访问 SAP HANA 和应用程序存储库、高可用性存储库以及 RHEL E4S 存储库。 在基础计算费用的基础上，计费包括 RHEL premium、SAP premium 和高可用性高级版。
|RedHat | RHEL | RHEL-HA | RHEL 次要版本和发布日期的串联值（例如，7.6.2019062019） | 这些映像是 RHEL 映像，也有权访问高可用性附加项。 由于高可用性附加项高级版，在 RHEL 和基础计算费用的基础上，它们会稍微额外收费。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和发布日期的串联值（例如，7.3.2017053118） | 这些映像已过期，因为 SAP 应用程序和 SAP HANA 存储库已合并到 SAP 存储库中。 这些映像是 RHEL for SAP 应用程序映像。 他们有权访问 SAP 应用程序存储库和基本 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和发布日期的串联值（例如，7.3.2018051421） | 这些映像已过期，因为 SAP 应用程序和 SAP HANA 存储库已合并到 SAP 存储库中。 这些映像是 RHEL for SAP HANA 映像。 他们有权访问 SAP HANA 存储库和基本 RHEL 存储库。

## <a name="rhel-8-image-types"></a>RHEL 8 映像类型

下面是 RHEL 8 映像类型的详细信息。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本和发布日期的串联值（例如，8.0.20191023） | 这些映像是已连接到标准 Red Hat 存储库的 RHEL 8.0 LVM 分区映像。
|RedHat | RHEL | 8-gen2 | RHEL 次要版本和发布日期的串联值（例如，8.0.20191024） | 这些映像是连接到标准 Red Hat 存储库的 Hyper-v 第 2 RHEL 8.0 LVM 分区映像。 有关 Azure 中第2代 Vm 的详细信息，请参阅[azure 上的第2代 Vm 支持](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)。

## <a name="rhel-longer-support-add-ons"></a>RHEL 更长的支持外接程序

### <a name="extended-update-support"></a>扩展更新支持

从2019年4月起，默认情况下，RHEL 映像可用于附加到 EUS 存储库。 有关 RHEL EUS 的详细信息，请[参阅 Red Hat 的文档](https://access.redhat.com/articles/rhel-eus)。

可以切换到 EUS 存储库，并且支持。 有关如何将 VM 切换到 EUS 的说明，以及有关 EUS 支持生命周期的详细信息，请参阅[RHEL EUS 和版本-锁定 RHEL vm](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)。

>[!NOTE]
> RHEL 附加上不支持 EUS。 这意味着，如果你安装的包通常在 RHEL 额外通道中可用，则无法在 EUS 上执行此操作。 有关 Red Hat 额外产品生命周期的详细信息，请参阅[Red Hat Enterprise Linux 额外生命周期](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiate-between-regular-and-eus-images"></a>区分常规映像和 EUS 映像

如果客户想要使用附加到 EUS 存储库的映像，则应在 SKU 中使用包含 RHEL 次版本号的 RHEL 映像。

例如，你可能会看到以下两个 RHEL 7.4 映像可用。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

在这种情况下，默认情况下 `RedHat:RHEL:7.6:7.6.2019102813` 附加到 EUS 存储库。 SKU 值为7.4。 默认情况下，`RedHat:RHEL:7-LVM:7.6.2019062414` 会附加到 EUS 存储库。 SKU 值为 7-LVM。

若要使用常规（非 EUS）存储库，请使用 SKU 中不包含次版本号的映像。

#### <a name="rhel-images-with-eus"></a>具有 EUS 的 RHEL 映像

下表中的信息适用于连接到 EUS 存储库的 RHEL 映像。

>[!NOTE]
> 撰写本文时，只有 RHEL 7.4 和更高版本的 EUS 支持。 RHEL < = 7.3 不再支持 EUS。
>
> 有关 RHEL EUS 可用性的详细信息，请参阅[Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。

次版本 |EUS 图像示例              |EUS 状态                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7。4      |RedHat： RHEL：7.4：7.4.2019041718 | 默认情况下，EUS 2019 及更高版本发布的映像。|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 默认情况下，EUS 2019 及更高版本发布的映像。 |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 默认情况下，发布的映像可能为2019和更高版本 EUS。 |
RHEL 8。0      |空值                            | Red Hat 中没有可用的 EUS。                               |

### <a name="update-services-for-sap"></a>更新 SAP 服务

最新的 RHEL for SAP 映像将连接到 SAP 解决方案的更新服务订阅（E4S）。 有关 E4S 的详细信息，请参阅 Red Hat[文档](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>具有 E4S 的 RHEL 映像

在2019年12月之后创建的以下提供的映像连接到 E4S 存储库：

* RHEL-SAP （RHEL for SAP）
* RHEL-SAP-HA （RHEL，适用于具有高可用性和更新服务的 SAP）

## <a name="other-available-offers-and-skus"></a>其他可用产品和 Sku

可用产品/服务和 Sku 的完整列表可能包括上表中列出的其他映像。 示例为 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 这些产品/服务可用于为特定 marketplace 解决方案提供支持。 也可以将其发布以供预览和测试之用。 它们可能会随时更改或删除，而不会出现警告。 除非 Microsoft 或 Red Hat 公开记录了这些文件，否则不要使用它们。

## <a name="publishing-policy"></a>发布策略

随着特定的常见漏洞和泄密（标识符）或偶尔的配置更改或更新的发生，Microsoft 和 Red Hat 将更新映像作为新的次要版本。 我们在三个工作日内尽快提供经过更新的映像，并遵循 CVE 修补程序的发行版或可用性。

我们仅更新给定映像系列中的当前次要版本。 随着较新次要版本的发布，我们将停止更新较旧的次要版本。 例如，在版本 RHEL 7.6 中，RHEL 7.5 映像将不再更新。

>[!NOTE]
> 从 RHEL 即用即付映像预配的活动 Azure Vm 已连接到 Azure RHUI，并可在通过 Red Hat 发布更新和修补程序后立即接收，并将其复制到 Azure RHUI。 按 Red Hat 进行正式发布后，时间通常小于24小时。 这些 Vm 不需要新发布的映像即可获取更新。 客户可以完全控制何时开始更新。

## <a name="image-retention-policy"></a>映像保留策略

当前策略是保留以前发布的所有映像。 我们保留删除已知会导致任何问题的映像的权利。 例如，可能会删除由于后续平台或组件更新而导致配置不正确的映像。 可能会删除的映像遵循当前的 Azure Marketplace 策略，在图像删除之前的30天内提供通知。

## <a name="next-steps"></a>后续步骤

* 若要查看 Azure 中 RHEL 映像的完整列表，请参阅[azure 中提供 Red Hat Enterprise Linux （RHEL）映像](./redhat-imagelist.md)。
* 若要了解有关 Azure Red Hat 更新基础结构的详细信息，请参阅[适用于 azure 中按需的 RHEL vm 的 Red Hat 更新基础结构](https://aka.ms/rhui-update)。
* 若要了解有关 RHEL BYOS 产品/服务的详细信息，请参阅[在 Azure 中 Red Hat Enterprise Linux 自带订阅金牌映像](./byos.md)。
* 有关适用于所有版本 RHEL 的 Red Hat 支持策略的信息，请参阅[Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。
