---
title: Azure Red Hat OpenShift 支持生命周期
description: 了解 Azure Red Hat OpenShift 的支持生命周期和受支持的版本
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: c066931cece60d14767b86254020ea5ba4bad1be
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854500"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 的支持生命周期

Red Hat 每三个月 (OCP) 约每三个月发布一次 Red Hat OpenShift 容器平台。 这些版本包括新增功能和改进。 修补程序版本经常 (每周) ，并且仅适用于次要版本中的关键 bug 修复。 这些修补程序版本可能包含安全漏洞或主要 bug 的修补程序。

Azure Red Hat OpenShift 是从 OCP 的特定版本生成的。 本文介绍了 Azure Red Hat OpenShift 支持的 OCP 版本以及有关升级、弃用功能和支持策略的详细信息。

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift 版本

Red Hat OpenShift 容器平台使用语义版本控制。 语义版本控制使用不同级别的版本号来指定不同的版本控制级别。 下表说明了语义版本号的不同部分，在这种情况下，使用示例版本号4.4.11。

|主版本 (x) |次版本 (y) |修补 (z) |
|-|-|-|
|4|4|11|

版本中的每个编写表示与前一版本的一般兼容性：

* **主要版本**：目前没有计划的主要版本。 如果发生不兼容的 API 更改或者后向兼容性受到破坏，主要版本将会更改。
* **次版本**：大约每三个月发布一次。 次版本升级可包括新增功能、增强功能、弃用功能、删除、bug 修复、安全增强功能以及其他改进功能。
* **修补程序**：通常每周发布一次，或根据需要发布。 修补程序版本升级可以包含 bug 修复、安全增强功能以及其他改进功能。

客户应瞄准它们运行的主版本的最新次要版本。 例如，如果生产群集处于4.4，而4.5 是4系列的最新正式发布的次要版本，则应尽快升级到4.5。

### <a name="upgrade-channels"></a>升级通道

升级通道绑定到 Red Hat OpenShift 容器平台的次版本， (OCP) 。 例如，OCP 4.4 升级通道绝不会升级到4.5 版本。 仅限升级通道控制发布选择，不会影响群集的版本。

Azure Red Hat OpenShift 4 仅支持稳定通道。 例如：稳定-4.4。

可以使用稳定-4.5 通道从 Azure Red Hat OpenShift 的以前次版本升级。 不支持使用快速、预发行和候选通道升级的群集。

如果你更改为不包含当前版本的通道，则会显示警报，并且不建议更新，但你可以在任何时候安全地改回原始通道。

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift 容器平台版本支持策略

Azure Red Hat OpenShift 支持两个正式上市 (GA) Red Hat OpenShift 容器平台的次要版本：
* 在 Azure Red Hat OpenShift 中发布的最新 GA 次版本，将 (为 N) 
* 前一个次要版本 (N-1) 

如果在稳定升级通道中提供，较新的次要版本 (N + 1，则还支持上游 OCP 中提供的 N + 2) 。

关键修补程序更新会由 Azure Red Hat OpenShift 站点可靠性工程师 (SRE) 自动应用到群集。 希望事先安装修补程序更新的客户可随意执行此操作。

例如，如果 Azure Red Hat OpenShift 现在引入 4.5. z，则为以下版本提供支持：

|新的次要版本|支持的版本列表|
|-|-|
|4.5. z|4.5. z，4.4 z|

"z" 代表修补程序版本。 如果在稳定升级通道中可用，则客户还可以升级到 4.6. z。

引入新的次要版本时，将弃用并删除最早的次要版本。 例如，假设当前支持的版本列表为 4.5. z 和4.4。 当 Azure Red Hat OpenShift 释放4.6 时，将删除4.4 版本并在30天内不受支持。

> [!NOTE]
> 请注意，如果客户运行的 Red Hat OpenShift 版本不受支持，则在请求支持群集时可能会要求升级它们。 运行不受支持的 Red Hat OpenShift 版本的群集不包含在 Azure Red Hat OpenShift SLA 中。

## <a name="release-and-deprecation-process"></a>发布和弃用流程

可以在 Azure Red Hat OpenShift Release Calendar 上参考即将发布的版本和弃用功能。

对于 Red Hat OpenShift 容器平台的新次要版本：
* Azure Red Hat OpenShift SRE 团队发布了一个预发布版，其中包含新版本发布的计划日期，并在删除之前的30天内对 [Azure Red Hat OpenShift 发行说明](https://github.com/Azure/OpenShift/releases) 进行了相应的旧版本弃用。
* Azure Red Hat OpenShift SRE 团队发布了一个服务运行状况通知，该通知适用于具有 Azure Red Hat OpenShift 和门户访问权限的所有客户，并向订阅管理员发送一封电子邮件，其中包含计划的版本删除日期。
* 客户从版本删除30天后，升级到受支持的次要版本以继续获得支持。

对于 Red Hat OpenShift 容器平台的新修补程序版本：
* 由于修补程序版本的紧急性质，Azure Red Hat OpenShift SRE 团队在提供服务时，这些版本可能会被引入到服务中。
* 通常，在安装新的修补程序版本时，Azure Red Hat OpenShift SRE 团队不会执行广泛的通信。 但是，团队不断监视和验证可用的 CVE 修补程序，以及时支持这些修补程序。 如果需要客户操作，团队将通知客户有关升级的信息。

## <a name="supported-versions-policy-exceptions"></a>支持的版本策略例外情况

Azure Red Hat OpenShift SRE 团队预留添加或删除新的/现有版本或延迟即将发布的次要发行版的权限，这些版本已被标识为有一个或多个关键生产影响 bug 或安全问题，且未提前通知。

可以跳过特定修补程序版本，也可以根据 bug 或安全问题的严重性加快推出。

## <a name="azure-portal-and-cli-versions"></a>Azure 门户和 CLI 版本

当你在门户中或使用 Azure CLI 部署 Azure Red Hat OpenShift 群集时，群集将默认为最新的 (N) 次版本和最新的关键修补程序。 例如，如果 Azure Red Hat OpenShift 支持 4.5. z 和 4.4 z，则新安装的默认版本为 4.5. z。 如果客户想要使用最新的上游 OCP 次版本 (N + 1，N + 2) 可随时将其群集升级到稳定升级通道中提供的任何版本。

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift 发行日历

请参阅以下指南，了解 [过去的 Red Hat OpenShift 容器平台 (上游) 发行历史记录](https://access.redhat.com/support/policy/updates/openshift/#dates)。

|OCP 版本|上游版本|Azure Red Hat OpenShift 公开上市|生命周期终止|
|-|-|-|-|
|4.3|2020 年 2 月|2020 年 5 月|2020 年 8 月|
|4.4|2020 年 5 月|2020 年 8 月|4.6 GA|
|4.5|2020 年 7 月|2020年10月|4.7 GA
|4.6|* 第四季度初期，2020|* 第四季度晚，2020|4.8 GA|

\*_挂起的上游发布日期确认。_

## <a name="faq"></a>常见问题解答

**如果用户使用不受支持的次要版本升级 OpenShift 群集，会发生什么情况？**

如果你处于 N-2 版或更低版本，则表示你超出支持范围，并将要求你进行升级。 从第2版升级到 N-1 成功升级后，你将返回到我们的支持策略中。 例如：
* 如果最早支持的 Azure Red Hat OpenShift 版本为 4.4. z，而你在4.3 或更低版本，则表示你超出了支持范围。
* 如果成功地从4.3 升级到4.4 或更高版本，则会返回到我们的支持策略中。

不支持将群集恢复到以前的版本或回滚。 仅支持升级到较新版本。

**"支持外" 的含义是什么？**

"不受支持" 表示你正在运行的版本在受支持的版本列表之外，请求支持时可能会要求你将群集升级到受支持的版本，除非你在版本弃用后的30天宽限期内。 此外，在30天的宽限期结束时，Azure Red Hat OpenShift 不会对支持的版本列表之外的群集进行任何运行时或 SLA 保证。
