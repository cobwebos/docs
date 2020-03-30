---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 了解 Microsoft Azure 中的红帽企业 Linux 映像。
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
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239874"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>红帽企业Linux映像概述

本文介绍了 Azure 应用商店中的可用红帽企业 Linux （RHEL） 映像及其命名和保留策略。

有关所有版本的 RHEL 的红帽支持策略的信息，请参阅[红帽企业 Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。 有关定价详细信息，请参阅[Azure 定价计算器](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> Azure 应用商店中当前可用的 RHEL 映像支持自带订阅 （BYOS） 或即用即付许可模型。 不支持在 BYOS 和即用即付许可之间使用[Azure 混合使用优势](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)和动态切换。 要切换许可模式，必须从相应的映像重新部署 VM。

>[!NOTE]
> 对于 Azure 应用商店中与 RHEL 映像相关的任何问题，向 Microsoft 提交支持票证。

## <a name="view-images-available-in-azure"></a>查看 Azure 中可用的图像

在 Azure 应用商店中搜索"红帽"或在 Azure 门户 UI 中创建资源时，将仅看到所有可用 RHEL 映像的子集。 始终可以使用 Azure CLI、PowerShell 和 API 获取完整的可用 VM 映像集。

要查看 Azure 中完整的可用红帽映像集，运行以下命令：

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名约定

Azure 中的 VM 映像按发布者、产品/服务、SKU 和版本组织。 发布者:产品/服务:SKU:版本的组合即为映像 URN，可唯一地标识要使用的映像。

例如，`RedHat:RHEL:8-LVM:8.1.20200318`是指 2020 年 3 月 18 日构建的 RHEL 8.1 LVM 分区映像。

此处显示了如何创建 RHEL 8.1 VM 的示例。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>“latest”名字对象

Azure REST API 允许对版本使用"最新"名字，而不是特定版本。 使用"最新"规定给定发布商、产品/服务/服务/SKU 的最新可用映像。

例如，`RedHat:RHEL:8-LVM:latest`引用最新的 RHEL 8 系列 LVM 分区映像可用。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 一般情况下，通过比较版本来确定最新版本时应遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)的规则。
此图像版本比较是通过将值作为[Version](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8)对象而不是字符串进行比较来完成的。

## <a name="rhel-6-image-types"></a>RHEL 6 图像类型

对于 RHEL 6.x 图像，下图显示了图像类型。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次要版本（例如 6.9） | RHEL 次要版本的连体值和发布日期（例如，6.9.2018010506） | 所有标准 RHEL 6.x 图像都遵循此约定。
|RedHat | 雷尔-比奥斯 | rhel-raw69 | RHEL 次要版本的连体值和发布日期（例如，6.9.20181023） | 此映像是 RHEL 6.9 BYOS 映像。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的连体值和发布日期（例如，6.8.2017053118） | 此映像是 SAP 应用程序映像的 RHEL 6.8。 它有权访问 SAP 应用程序存储库和基础 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的连体值和发布日期（例如，6.7.2017053121） | 此图像是 SAP HANA 图像的 RHEL 6.7。 它有权访问 SAP HANA 存储库和基础 RHEL 存储库。

## <a name="rhel-7-image-types"></a>RHEL 7 图像类型

对于 RHEL 7.x 图像，有几种不同的图像类型。 下表显示了我们提供的不同图像集。 要查看完整列表，请使用 Azure CLI 命令`az vm image list --publisher redhat --all`。

>[!NOTE]
> 除非另有说明，否则所有图像均被 LVM 分区并连接到常规 RHEL 存储库。 也就是说，存储库不是扩展更新支持 （EUS），也不是 SAP 的更新服务 （E4S）。 展望未来，我们将只发布 LVM 分区映像，但可以收到有关此决策的反馈。 有关 SAP 扩展更新支持和更新服务的详细信息，请参阅[红帽企业 Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次要版本（例如，7.6） | RHEL 次要版本的连体值和发布日期（例如，7.6.2019102813） | 2019 年 4 月之前发布的图像将附加到标准 RHEL 存储库。 2019 年 4 月之后发布的图像将附加到红帽的 EUS 存储库，以允许特定次要版本的版本锁定。 需要常规存储库的客户应使用 SKU 值中包含 7-LVM 或 7-RAW 的图像（详情如下）。 RHEL 7.7 和更高版本的图像被 LVM 分区。 此类别中的所有其他映像都是原始分区的。
|RedHat | RHEL | 7-RAW | RHEL 次要版本的连体值和发布日期（例如，7.6.2019102813） | 这些映像是原始分区的（例如，未添加逻辑卷）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本的连体值和发布日期（例如，7.6.2019072418） | 这些映像是原始分区的（例如，未添加逻辑卷），并使用云 init 进行预配。
|RedHat | RHEL | 7-LVM | RHEL 次要版本的连体值和发布日期（例如，7.6.2019062414） | 这些映像已分区 LVM。
|RedHat | 雷尔-比奥斯 | rhel-lvm，原始* | RHEL 次要版本的连体值和发布日期（例如，7.7.20190819） | 这些图像是 RHEL 7 BYOS 图像。 它们不附加到任何存储库，也不会收取 RHEL 保费。 如果您对 RHEL BYOS 映像感兴趣，[请请求访问](https://aka.ms/rhel-byos)。 SKU 值以次要版本结尾，表示映像是生的还是对 LVM 分区的。 例如，rhel-lvm77 的 SKU 值表示 LVM 分区的 RHEL 7.7 图像。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本的连体值和发布日期（例如，7.6.2019071300） | 这些映像是 SAP 映像的 RHEL。 他们有权访问 SAP HANA 和应用程序存储库以及 RHEL E4S 存储库。 计费包括 RHEL 保费和 SAP 保费，以及基本计算费用。
|RedHat | RHEL | 雷尔-萨普哈 | RHEL 次要版本的连体值和发布日期（例如，7.6.2019062320） | 这些映像是具有高可用性和更新服务映像的 SAP 的 RHEL。 他们有权访问 SAP HANA 和应用程序存储库、高可用性存储库以及 RHEL E4S 存储库。 计费包括 RHEL 保费、SAP 保费和高可用性溢价，以及基本计算费用。
|RedHat | RHEL | 雷尔-哈 | RHEL 次要版本的连体值和发布日期（例如，7.6.2019062019） | 这些图像是 RHEL 图像，也有权访问高可用性加载项。 由于高可用性附加溢价，它们在 RHEL 和基本计算费用上收取稍多的费用。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的连体值和发布日期（例如，7.3.2017053118） | 这些映像已过期，因为 SAP 应用程序和 SAP HANA 存储库已合并到 SAP 存储库中。 这些映像是 SAP 应用程序映像的 RHEL。 他们有权访问 SAP 应用程序存储库和基础 RHEL 存储库。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的连体值和发布日期（例如，7.3.2018051421） | 这些映像已过期，因为 SAP 应用程序和 SAP HANA 存储库已合并到 SAP 存储库中。 这些图像是 SAP HANA 图像的 RHEL。 他们有权访问 SAP HANA 存储库和基础 RHEL 存储库。

## <a name="rhel-8-image-types"></a>RHEL 8 图像类型

RHEL 8 图像类型的详细信息如下。

|发布者 | 产品/服务 | SKU 值 | 版本 | 详细信息
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本的连体值和发布日期（例如，8.0.20191023） | 这些图像是 RHEL 8 LVM 分区的图像，连接到标准红帽存储库。
|RedHat | RHEL | 8 代2 | RHEL 次要版本的连体值和发布日期（例如，8.0.20191024） | 这些图像是 Hyper-V 第 2 代 RHEL 8 LVM 分区的图像，连接到标准红帽存储库。 有关 Azure 中第 2 代 VM 的详细信息，请参阅[Azure 上对第 2 代 VM 的支持](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)。

## <a name="rhel-longer-support-add-ons"></a>RHEL 长支持附加组件

### <a name="extended-update-support"></a>扩展更新支持

自 2019 年 4 月起，RHEL 图像默认连接到 EUS 存储库。 有关 RHEL EUS 的更多信息，请参阅[红帽的文档](https://access.redhat.com/articles/rhel-eus)。

可以切换到 EUS 存储库，并且支持。 有关如何将 VM 切换到 EUS 的说明以及有关 EUS 支持寿命终止日期的详细信息，请参阅[RHEL EUS 和版本锁定 RHEL VM。](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)

>[!NOTE]
> RHEL 附加服务不支持 EUS。 这意味着，如果您安装的包通常可从 RHEL Extras 通道获得，则在 EUS 上时将无法执行此操作。 有关红帽附加产品生命周期的详细信息，请参阅[红帽企业 Linux 附加生命周期](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiate-between-regular-and-eus-images"></a>区分常规图像和 EUS 图像

希望使用附加到 EUS 存储库的图像的客户应使用 SKU 中包含 RHEL 次要版本号的 RHEL 映像。

例如，您可能会看到以下两个 RHEL 7.4 图像可用。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

在这种情况下，`RedHat:RHEL:7.6:7.6.2019102813`默认情况下附加到 EUS 存储库。 SKU 值为 7.4。 默认情况下`RedHat:RHEL:7-LVM:7.6.2019062414`，它附加到非 EUS 存储库。 SKU 值为 7-LVM。

要使用常规（非 EUS）存储库，请使用 SKU 中不包含次要版本号的图像。

#### <a name="rhel-images-with-eus"></a>带 EUS 的 RHEL 图像

下表中的信息适用于连接到 EUS 存储库的 RHEL 映像。

>[!NOTE]
> 在编写本文时，只有 RHEL 7.4 和更高版本的次要版本才支持 EUS。 RHEL <+7.3 不再支持 EUS。
>
> 有关 RHEL EUS 可用性的详细信息，请参阅[红帽企业 Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。

次版本 |EUS 图像示例              |EUS 状态                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |红帽：RHEL：7.4：4.2019041718 | 默认情况下，2019 年 4 月及更高版本发布的图像为 EUS。|
RHEL 7.5      |红帽：RHEL：7.5：7.5.2019060305 | 默认情况下，2019 年 6 月及更高版本发布的图像为 EUS。 |
RHEL 7.6      |红帽：RHEL：7.6：7.6.2019052206 | 默认情况下，2019 年 5 月及更高版本发布的图像为 EUS。 |
RHEL 8.0      |空值                            | 红帽没有EUS。                               |

### <a name="update-services-for-sap"></a>更新 SAP 服务

SAP 映像的最新 RHEL 将连接到 SAP 解决方案订阅的更新服务 （E4S）。 有关 E4S 的详细信息，请参阅红帽[文档](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>带 E4S 的 RHEL 图像

2019 年 12 月以后创建的以下优惠中的图像已连接到 E4S 存储库：

* RHEL-SAP（用于 SAP 的 RHEL）
* RHEL-SAP-HA（用于高可用性和更新服务的 SAP RHEL）

## <a name="other-available-offers-and-skus"></a>其他可用优惠和 SKU

可用优惠和 SKU 的完整列表可能包括超出上表中列出的其他映像。 示例为 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 这些优惠可用于为特定市场解决方案提供支持。 或者，它们可以发布用于预览和测试目的。 它们可能随时更改或删除，恕不经警告。 除非 Microsoft 或红帽公开记录它们的存在，否则不要使用它们。

## <a name="publishing-policy"></a>发布策略

Microsoft 和红帽会根据解决特定常见漏洞和暴露 （CVE） 或偶尔更改或更新的配置更新，以发布新的次要版本来更新映像。 我们努力在 CVE 修复程序发布或可用后的三个工作日内尽快提供更新的图像。

我们仅更新给定映像系列中的当前次要版本。 随着较新次要版本的发布，我们将停止更新较旧的次要版本。 例如，随着 RHEL 7.6 的发布，RHEL 7.5 图像将不再更新。

>[!NOTE]
> 从 RHEL 即付即用映像预配的活动 Azure VM 连接到 Azure RHUI，一旦由红帽发布并复制到 Azure RHUI，即可接收更新和修复。 时间通常不到24小时后，红帽正式释放。 这些 VM 不需要新的已发布映像来获取更新。 客户可以完全控制何时启动更新。

## <a name="image-retention-policy"></a>映像保留策略

当前策略是保留以前发布的所有图像。 我们保留删除已知会导致任何问题的映像的权利。 例如，由于后续平台或组件更新而配置不正确的映像可能会被删除。 可能删除的图像遵循当前的 Azure 应用商店策略，在删除映像前 30 天提供通知。

## <a name="next-steps"></a>后续步骤

* 要查看 Azure 中 RHEL 映像的完整列表，请参阅[Azure 中可用的红帽企业 Linux （RHEL） 映像](./redhat-imagelist.md)。
* 要了解有关 Azure 红帽更新基础结构的详细信息，请参阅[红帽更新基础结构，了解 Azure 中的按需 RHEL VM。](https://aka.ms/rhui-update)
* 要了解有关 RHEL BYOS 产品/服务详细信息，请参阅[Azure 中的红帽企业 Linux 自编自办黄金映像](./byos.md)。
* 有关所有版本的 RHEL 的红帽支持策略的信息，请参阅[红帽企业 Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。
