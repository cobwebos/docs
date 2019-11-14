---
title: Azure 中的 Red Hat Enterprise Linux 映像
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
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: eaabe9da20c22dd3e4d924887adcbc7081857e91
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035115"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Azure 中的 Red Hat Enterprise Linux 映像
本文介绍 Azure 市场中可用的 Red Hat Enterprise Linux (RHEL) 映像以及与其命名和保留相关的策略。

可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。

>[!Important]
> Azure 市场中当前提供的 RHEL 映像支持自带订阅 (BYOS) 或即用即付 (PAYG) 许可模型。 不支持 [Azure 混合使用权益](../windows/hybrid-use-benefit-licensing.md)以及 BYOS 和 PAYG 之间的动态切换。 切换许可模式需要从相应的映像重新部署 VM。

>[!Note]
> 有关 Azure 市场库中与 RHEL 映像相关的任何问题，请向 Microsoft 提交支持票证。

## <a name="images-available-in-the-ui"></a>UI 中可用的映像
在市场中搜索“Red Hat”或在 Azure 门户 UI 中创建资源时，可看到一部分可用的 RHEL 映像和相关的 Red Hat 产品。 只要使用 Azure CLI/PowerShell/API 就能获取完整的可用 VM 映像集。

若要在 Azure 中查看完整的可用 Red Hat 映像集，请运行以下命令

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>命名约定
Azure 中的 VM 映像按发布者、产品/服务、SKU 和版本进行组织。 发布者:产品/服务:SKU:版本的组合即为映像 URN，可唯一地标识要使用的映像。

例如，`RedHat:RHEL:7-RAW:7.6.2018103108` 表示 2018 年 10 月 31 日生成的 RHEL 7.6 原始分区映像。

下面展示了有关如何创建 RHEL 7.6 VM 的示例。
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>“latest”名字对象
Azure REST API 允许使用名字对象“latest”作为版本来代替特定版本。 使用“latest”将为给定的发布者、产品/服务和 SKU 预配最新的可用映像。

例如，`RedHat:RHEL:7-RAW:latest` 表示可用的最新 RHEL 7 系列原始分区映像。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> 一般情况下，通过比较版本来确定最新版本时应遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)的规则。

### <a name="current-naming-convention"></a>当前的命名约定
当前发布的所有 RHEL 映像均使用即用即付模型，并连接到 [Azure 中的 Red Hat 更新基础结构 (RHUI)](https://aka.ms/rhui-update)。 RHEL 7 系列映像采用了新的命名约定，其中在 SKU 而不是版本中指定了磁盘分区方案（原始、LVM）。 RHEL 映像版本包含 7-RAW 或 7-LVM。 RHEL 6 系列的命名目前尚未更改。

此命名约定中将有2种类型的 RHEL 7 映像 Sku：列出次要版本的 Sku 和未列出的 Sku。 如果要使用 7-RAW 或 7-LVM SKU，可以指定要在版本中部署的 RHEL 次要版本。 如果选择 "最新版本"，则将预配 RHEL 的最新次要版本。

>[!NOTE]
> 在 RHEL for SAP 映像集中，RHEL 版本保持不变。 因此，它们的命名约定在 SKU 中包含特定版本。

>[!NOTE]
> RHEL 6 映像集未改用新的命名约定。

## <a name="extended-update-support-eus"></a>扩展更新支持（EUS）
从2019年4月起，默认情况下，RHEL 映像可用于附加到扩展更新支持（EUS）存储库。 有关 RHEL EUS 的更多详细信息，请[参阅 Red Hat 的文档](https://access.redhat.com/articles/rhel-eus)。

[此处](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)提供了有关如何将 VM 切换到 EUS 的说明，以及有关 EUS 支持的更多详细信息。

>[!NOTE]
> RHEL 附加上不支持 EUS。 这意味着，如果你要安装的包通常在 RHEL 额外通道中可用，则在 EUS 上将无法执行此操作。 [此处](https://access.redhat.com/support/policy/updates/extras/)详细介绍了 Red Hat 额外产品生命周期。

### <a name="for-customers-that-want-to-use-eus-images"></a>对于想要使用 EUS 映像的客户：
如果客户想要使用附加到 EUS 存储库的映像，则应在 SKU 中使用包含 RHEL 次版本号的 RHEL 映像。 这些映像将是原始分区（即非 LVM）。

例如，你可能会看到以下 2 RHEL 7.4 映像可用：
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
在这种情况下，默认情况下 `RedHat:RHEL:7.4:7.4.2019041718` 会附加到 EUS 存储库，默认情况下 `RedHat:RHEL:7-RAW:7.4.2018010506` 会附加到非 EUS 存储库。

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>对于不希望使用 EUS 映像的客户：
如果不想使用默认情况下连接到 EUS 的映像，请使用不包含 SKU 中的次要版本号的映像进行部署。

#### <a name="rhel-images-with-eus"></a>具有 EUS 的 RHEL 映像
下表适用于在 SKU 中包含次要版本的 RHEL 映像。

>[!NOTE]
> 撰写本文时，只有 RHEL 7.4 和更高版本的 EUS 支持。 RHEL < 不再支持 EUS = 7.3。
>
> 有关 RHEL EUS 可用性的详细信息，请参阅[此处](https://access.redhat.com/support/policy/updates/errata)。

次版本 |EUS 图像示例              |EUS 状态                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7。4      |RedHat： RHEL：7.4：7.4.2019041718 | 默认情况下，将 EUS 2019 年4月版和更高版本发布的映像|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 默认情况下，将 EUS 2019 年6月发布的映像 |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 默认情况下，发布的映像可能为2019，以后将 EUS  |
RHEL 8。0      |不适用                            | Red Hat 中没有可用的 EUS                               |


## <a name="list-of-rhel-images-available"></a>可用的 RHEL 映像列表
以下产品/服务和 SKU 目前可用于一般用途：

产品/服务| SKU | 分区 | 设置 | 说明
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux 代理 | RHEL 7. x 映像系列。 <br> 默认情况下未附加到 EUS 存储库。
|             | 7-LVM    | LVM    | Linux 代理 | RHEL 7. x 映像系列。 <br> 默认情况下未附加到 EUS 存储库。
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7. x 映像系列。 <br> 默认情况下未附加到 EUS 存储库。
|             | 6.7      | RAW    | Linux 代理 |
|             | 6.8      | RAW    | Linux 代理 |
|             | 6.9      | RAW    | Linux 代理 |
|             | 6.10     | RAW    | Linux 代理 |
|             | 7.2      | RAW    | Linux 代理 |
|             | 7.3      | RAW    | Linux 代理 |
|             | 7.4      | RAW    | Linux 代理 | 默认情况下，在2019年4月之前附加到 EUS 存储库。
|             | 7.5      | RAW    | Linux 代理 | 默认情况下，附加到 EUS 存储库，截至2019年6月。
|             | 7.6      | RAW    | Linux 代理 | 默认情况下，附加到 EUS 存储库，可能为2019。
|             | 7.7      | LVM    | Linux 代理 | 默认情况下附加到 EUS 存储库。
RHEL-SAP      | 7.4      | LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.4
|             | 7.5      | LVM    | Linux 代理 | 用于 SAP HANA 和 Business Apps 的 RHEL 7.5
RHEL-SAP-HANA | 6.7      | RAW    | Linux 代理 | 用于 SAP HANA 的 RHEL 6.7
|             | 7.2      | LVM    | Linux 代理 | 用于 SAP HANA 的 RHEL 7.2
|             | 7.3      | LVM    | Linux 代理 | 用于 SAP HANA 的 RHEL 7.3
RHEL-SAP-APPS | 6.8      | RAW    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 6.8
|             | 7.3      | LVM    | Linux 代理 | 用于 SAP Business Applications 的 RHEL 7.3
RHEL-HA       | 7.4      | LVM    | Linux 代理 | RHEL 7.4 与 HA 加载项
|             | 7.5      | LVM    | Linux 代理 | RHEL 7.5 与 HA 加载项
|             | 7.6      | LVM    | Linux 代理 | RHEL 7.6 与 HA 加载项
RHEL-SAP-HA   | 7.4      | LVM    | Linux 代理 | RHEL 7.4 for SAP 与 HA 附加项
|             | 7.5      | LVM    | Linux 代理 | RHEL 7.5 for SAP 与 HA 附加项
|             | 7.6      | LVM    | Linux 代理 | RHEL 7.6 for SAP 与 HA 附加项

### <a name="old-naming-convention"></a>旧的命名约定
在发生上文所述的命名约定更改之前，RHEL 7 系列映像和 RHEL 6 系列映像一直在其 SKU 中使用特定版本。

可在完整映像列表中找到数字 SKU。 当新的次要版本推出时，Microsoft 和 Red Hat 将创建新的数字 SKU。

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
* 从[此处](https://aka.ms/rhui-update)了解有关 Azure Red Hat 更新基础结构的详细信息。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
