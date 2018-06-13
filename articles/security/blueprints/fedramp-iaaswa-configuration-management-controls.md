---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 配置管理
description: FedRAMP Web 应用程序自动化 - 配置管理
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 356eccac6af2780c02d1cd935d41891b5f89f1a2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206286"
---
# <a name="configuration-management-cm"></a>配置管理 (CM)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-cm-1"></a>NIST 800-53 控制措施 CM-1

#### <a name="configuration-management-policy-and-procedures"></a>配置管理策略和过程

**CM-1** 组织开发、记录和分发到[复制：组织定义的个人或角色]配置管理策略，该策略解决用途、范围、角色、职责、管理承诺、组织实体之间的协调和符合性；促进配置管理策略及其关联的配置管理控件的实施过程；检查和更新当前配置管理策略[赋值：组织定义的频率]；配置管理过程[赋值：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级配置管理策略和过程可能足以应对此控制措施。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800-53 控制措施 CM-2

#### <a name="baseline-configuration"></a>基线配置

**CM-2** 组织在配置控制措施（信息系统的当前基线配置）下进行开发、记录和维护。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure 资源管理器模板和构成此蓝图的附带资源表示已部署的体系结构的“作为代码的配置”基线。 解决方案通过 GitHub 提供，可以用于配置控制措施。 此解决方案包括每个已部署虚拟机的所需状态配置 (DSC) 基线。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800-53 控制措施 CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>基线配置 | 评审和更新

**CM-2 (1).a** 组织评审和更新信息系统的基线配置[赋值：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审和更新客户部署资源的基线配置（包括应用程序、操作系统、数据库和软件）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800-53 控制措施 CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>基线配置 | 评审和更新

**CM-2 (1).a** 组织在[赋值：组织定义的环境]需要时，评审和更新信息系统的基线配置。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在需要时评审和更新客户部署资源的基线配置。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800-53 控制措施 CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>基线配置 | 评审和更新

**CM-2 (1).c** 组织会在信息系统组件安装和升级过程中，评审和更新信息系统的基线配置。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在需要时评审和更新客户部署资源的基线配置。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 控制措施 CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>基线配置 | 准确度/货币的自动化支持

**CM-2 (2)** 组织采用自动化机制来维护信息系统基线配置的最新状态、完整性、准确性和随时可用性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure 资源管理器模板和构成此蓝图的附带资源表示已部署的体系结构的“作为代码的配置”基线。 解决方案通过 GitHub 提供，可以用于配置控制措施。 在 Azure 门户中，自动化脚本适用于所有已部署的资源，并始终提供这些资源的最新表示形式。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 控制措施 CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>基线配置 | 以前配置的保留

**CM-2 (3)** 组织保留[赋值：组织定义的信息系统基线配置的以前版本]以支持回滚功能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责保留客户部署资源的基线配置的以前版本。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 控制措施 CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>基线配置 | 为高风险区域配置系统、组件或设备

**CM-2 (7).a** 组织将[赋值：组织定义的信息系统、系统组件或设备]和[赋值：组织定义的配置]颁发给前往组织认为具有重大风险的位置的个人。

**责任：**`Not Applicable`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有客户控制的物理设备。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 的客户内容从未存储在 Microsoft Azure 之外，即物理上位于美国本土内部。 Microsoft Azure 人员旅行时，不携带 Microsoft Azure 清单中包含的设备。 因此，此控制措施不适用于 Microsoft Azure。 |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 控制措施 CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>基线配置 | 为高风险区域配置系统、组件或设备

**CM-2 (7).b** 组织在个人返回时对设备应用[赋值：组织定义的安全保护措施]。

**责任：**`Not Applicable`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内没有客户控制的物理设备。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 客户内容从未存储在 Microsoft Azure 之外，并且 Microsoft Azure 人员旅行时，不携带 Microsoft Azure 清单中包含的设备，因此此控制措施不适用。 |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800-53 控制措施 CM-3.a

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.a** 组织确定由配置控制的信息系统的变更类型。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责确定客户部署资源的哪种变更类型（包括应用程序、操作系统、数据库和软件）由配置控制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800-53 控制措施 CM-3.b

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.b** 组织评审建议的对信息系统的配置控制变更，并通过对安全影响分析的明确考量，批准或拒绝批准此类变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审对客户部署的资源所建议的配置控制变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800-53 控制措施 CM-3.c

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.c** 组织记录与信息系统相关的配置变更决策。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责记录与客户部署资源相关的配置控制变更（请参阅 CM-03.b）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800-53 控制措施 CM-3.d

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.d** 组织对信息系统实施批准的配置控制变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施在 CM-03.b 中批准的配置控制变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800-53 控制措施 CM-3.e

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.e** 组织在[赋值：组织定义的时间段]内保留信息系统的配置控制变更记录。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责保留客户部署资源的配置控制变更记录。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800-53 控制措施 CM-3.f

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.f** 组织审核和评审与信息系统的配置控制变更相关的活动。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责审核和评审配置变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800-53 控制措施 CM-3.g

#### <a name="configuration-change-control"></a>配置变更控制措施

**CM-3.g** 组织通过[选择（一个或多个）：[赋值：组织定义的频率]；[赋值：组织定义的配置变更条件]]召集的[赋值：组织定义的配置变更控制元素（例如，委员会、董事会）]对配置变更控制活动进行协调和监督。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责对配置变更控制活动进行协调和监督。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800-53 控制措施 CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>配置变更控制 | 自动记录/通知/禁止变更

**CM-3 (1).a** 组织采用自动化机制来记录信息系统的建议变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用自动化机制来记录建议的变更（请参阅 CM-03.b）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800-53 控制措施 CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>配置变更控制 | 自动记录/通知/禁止变更

**CM-3 (1).b** 组织采用自动化机制向[赋值：组织定义的审批机构]通知信息系统建议的变更，并请求变更批准。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用自动化机制来路由和请求对客户部署的资源进行变更的批准。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800-53 控制措施 CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>配置变更控制 | 自动记录/通知/禁止变更

**CM-3 (1).c** 组织采用自动化机制来突出显示[赋值：组织定义的时间段]内未通过的信息系统建议的变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用自动化机制来突出显示未评审的变更建议。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800-53 控制措施 CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>配置变更控制 | 自动记录/通知/禁止变更

**CM-3 (1).d** 组织采用自动化机制，在收到指定的批准之前禁止对信息系统进行变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用自动化机制来禁止对客户部署的资源实施未经批准的变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800-53 控制措施 CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>配置变更控制 | 自动记录/通知/禁止变更

**CM-3 (1).e** 组织采用自动化机制来记录对信息系统的所有变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用自动化机制来记录对客户部署的资源实施所有变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800-53 控制措施 CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>配置变更控制 | 自动记录/通知/禁止变更

**CM-3 (1).f** 当信息系统的已批准变更完成时，组织采用自动化机制通知[赋值：组织定义的人员]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 当客户部署资源的已批准变更完成时，客户负责使用自动化机制提供通知。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 控制措施 CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>配置变更控制 | 测试/验证/记录变更

**CM-3 (2)** 在实施操作系统变更之前，组织会测试、验证和记录对信息系统的变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在实施前，对客户部署的资源的变更进行测试、验证和记录。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 控制措施 CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>配置变更控制 | 安全代表

**CM-3 (4)** 组织要求信息安全代表成为[赋值：组织定义的配置更改控制元素]的成员。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责分配一个信息安全代表成为 CM-03.g 中定义的变更控制元素的成员。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 控制措施 CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>配置变更控制 | 加密管理

**CM-3 (6)** 组织确保用于提供[赋值：组织定义的安全保护措施]的加密机制受配置管理。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责确保加密机制受配置管理。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800-53 控制措施 CM-4

#### <a name="security-impact-analysis"></a>安全影响分析

**CM-4** 组织分析信息系统的变更，以确定变更实施前的潜在安全影响。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责分析对客户部署的资源建议的变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 控制措施 CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>安全影响分析 | 单独的测试环境

**CM-4 (1)** 组织在操作环境中实施变更之前，先在单独的测试环境中分析对信息系统的变更，寻找由于缺陷、缺点、不兼容性或故意的恶意操作造成的安全影响。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在操作环境中实施前，先在测试环境中分析对客户部署的资源进行的变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800-53 控制措施 CM-5

#### <a name="access-restrictions-for-change"></a>变更的访问限制

**CM-5** 组织定义、记录、批准并强制执行与信息系统变更相关的物理和逻辑访问限制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Active Directory 帐户特权是使用基于角色的访问控制实现的，通过将用户分配给角色来严格控制哪些用户可查看和控制已部署的资源。 通过将用户分配到安全组，使用基于角色的访问控制来实现 Active Directory 帐户特权。 这些安全组控制用户可以执行的有关操作系统配置的操作。 可以由客户扩展这些基于角色的方案以满足任务需求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 控制措施 CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>变更的访问限制 | 强制执行/审核自动化访问

**CM-5 (1)** 信息系统强制执行访问限制并支持审核强制执行操作。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Active Directory 帐户特权是使用基于角色的访问控制实现的，通过将用户分配给角色来严格控制哪些用户可查看和控制已部署的资源。 通过将用户分配到安全组，使用基于角色的访问控制来实现 Active Directory 帐户特权。 这些安全组控制用户可以执行的有关操作系统配置的操作。 所有访问和访问尝试都会被审核。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 控制措施 CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>变更的访问限制 | 查看系统变更

**CM-5 (2)** 组织查看信息系统变更[赋值：组织定义的频率]和[赋值：组织定义的环境]，以确定是否发生未授权的变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责查看客户部署资源的变更，以确定是否发生未授权的变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 控制措施 CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>变更的访问限制 | 已签名的组件

**CM-5 (3)** 如果未经验证组件是否使用组织认可和批准的证书进行数字签名，组织禁止安装[赋值：组织定义的软件和固件组件]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的虚拟机实施 Windows AppLocker，以指定哪些用户可以安装和/或运行特定应用程序。 此外，所有 Windows 操作系统的更新都经过数字签名。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 控制措施 CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>变更的访问限制 | 限制生产/操作权限

**CM-5 (5).a** 组织限制在生产或操作环境中更改信息系统组件和系统相关信息的权限。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责限制在客户部署的生产或操作环境中进行更改的权限。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 控制措施 CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>变更的访问限制 | 限制生产/操作权限

**CM-5 (5).b** 组织查看和重新评估权限[赋值：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责查看和重新评估在 CM-05(05).a 中定义的权限。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800-53 控制措施 CM-6.a

#### <a name="configuration-settings"></a>配置设置

**CM-6.a** 组织使用[赋值：组织定义的安全配置清单]（反映与操作要求一致的最严格模式），建立和记录信息系统中使用的信息技术产品的配置设置。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图包括每个已部署虚拟机的所需状态配置 (DSC) 基线。 这些声明性的 PowerShell 脚本定义和配置其所应用到的资源。 该解决方案部署的资源所包含的基线 DSC 可由客户扩展以满足任务需求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 控制措施 CM-6.b

#### <a name="configuration-settings"></a>配置设置

**CM-6.b** 组织实施配置设置。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图包括每个已部署虚拟机的所需状态配置 (DSC) 基线。 在部署过程中，使用自定义脚本虚拟机扩展将基线自动应用到虚拟机。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 控制措施 CM-6.c

#### <a name="configuration-settings"></a>配置设置

**CM-6.c** 组织根据[赋值：组织定义的操作需求]标识、记录和批准[赋值：组织定义的信息系统组件]的已建立的配置设置的任何偏差。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责标识、记录和批准客户部署资源的已建立的配置设置的任何偏差。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800-53 控制措施 CM-6.d

#### <a name="configuration-settings"></a>配置设置

**CM-6.d** 组织根据组织的策略和过程监视和控制对配置设置的变更。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Automation DSC。 Automation DSC 使计算机配置与特定的组织定义的配置一致。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 控制措施 CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>配置设置 | 自动化的中央管理/应用/验证

**CM-6 (1)** 组织采用自动化机制对[赋值：组织定义的信息系统组件]的配置设置进行集中管理、应用和验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Azure Automation DSC。 Automation DSC 使计算机配置与特定的组织定义的配置保持一致，并持续监视变更。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 控制措施 CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>配置设置 | 响应未经授权的变更

**CM-6 (2)** 组织使用[赋值：组织定义的安全保护措施]来响应对[赋值：组织定义的配置设置]的未经授权的更改。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Azure Automation DSC。 Automation DSC 可配置为在检测到错误配置时，生成警报或对其进行更正。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800-53 控制措施 CM-7.a

#### <a name="least-functionality"></a>最少的功能

**CM-7.a** 组织将信息系统配置为仅提供基本功能。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的资源配置为根据其预期用途提供最少功能。 每个已部署虚拟机的所需状态配置 (DSC) 基线包含在内。 这些声明性的 PowerShell 脚本定义和配置其所应用到的资源。 该解决方案部署的资源所包含的基线 DSC 可由客户扩展以根据任务需求进一步限制功能。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800-53 控制措施 CM-7.b

#### <a name="least-functionality"></a>最少的功能

**CM-7.b** 组织禁止或限制使用以下函数、端口、协议和/或服务：[赋值：组织定义的受禁止或限制的函数、端口、协议和/或服务]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署了 Azure 应用程序网关和网络安全组，以将对端口和协议的使用限制为仅允许使用必要的端口和协议。 虚拟机的应用程序网关、网络安全组和 DSC 基线可由客户进一步配置，以将函数、端口、协议和服务的使用限制为仅提供预期功能。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800-53 控制措施 CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>最少的功能 | 定期评审

**CM-7 (1).a** 组织评审信息系统[赋值：组织定义的频率]，以标识不必要和/或不安全的函数、端口、协议和服务。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审客户部署的资源（包括应用程序、操作系统、数据库和软件），以标识不必要和/或不安全的函数、端口、协议和服务。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800-53 控制措施 CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>最少的功能 | 定期评审

**CM-7 (1).b** 组织禁用[赋值：在信息系统内，被视为不必要和/或不安全的组织定义的函数、端口、协议和服务]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责禁用被视为不必要或不安全的函数、端口、协议和服务。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 控制措施 CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>最少的功能 | 防止程序执行

**CM-7 (2)** 信息系统根据[选择（一个或多个）：[赋值：组织定义的有关软件程序使用和限制的策略]；授权软件程序使用的条款和情况的规则]，阻止程序的执行。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责按照客户定义的软件程序使用策略，阻止程序执行。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 控制措施 CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>最少的功能 | 授权软件/白名单

**CM-7 (5).a** 组织标识[赋值：组织定义的经授权在信息系统上执行的软件程序]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责标识经授权的软件程序。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 控制措施 CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>最少的功能 | 授权软件/允许列表

**CM-7 (5).b** 组织采用全部拒绝、允许例外的策略，允许在信息系统上执行经过授权的软件程序。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用全部拒绝、允许例外的策略，允许在客户部署的资源上执行经过授权的软件程序。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 控制措施 CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>最少的功能 | 授权软件/允许列表

**CM-7 (5).c** 组织评审和更新经过授权的软件程序列表[赋值：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责评审和更新经过授权的软件程序列表。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800-53 控制措施 CM-8.a

#### <a name="information-system-component-inventory"></a>信息系统组件清单

**CM-8.a** 组织制定和记录能准确反映当前信息系统的信息系统组件的清单；包括信息系统授权范围内的所有组件；为跟踪和报告所需要的粒度级别；并且包括[赋值：被视为实施有效的信息系统组件问责制所需要的组织定义的信息]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图将所有资源部署到 Azure 资源管理器资源组。 Azure 资源管理器提供始终最新的部署资源列表，可以针对资源标记和分组进行自定义以便实现清单管理。 由此解决方案部署的资源会被赋予一个与系统边界相关的特定资源标记。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800-53 控制措施 CM-8.b

#### <a name="information-system-component-inventory"></a>信息系统组件清单

**CM-8.b** 组织评审和更新信息系统组件清单[赋值：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图将所有资源部署到 Azure 资源管理器资源组。 Azure 资源管理器提供可在 Azure 门户中查看的已部署资源的始终最新列表。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 控制措施 CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>信息系统组件清单 | 在安装/删除过程中更新

**CM-8 (1)** 组织在组件安装、删除和信息系统更新时，更新信息系统组件清单。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图将所有资源部署到 Azure 资源管理器资源组。 Azure 门户中的资源边栏选项卡列出了所有已部署的资源，在部署和删除资源时提供始终最新的清单。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 控制措施 CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>信息系统组件清单 | 自动维护

**CM-8 (2)** 组织使用自动化机制来维护信息系统组件清单的最新状态、完整性、准确性和随时可用性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图将所有资源部署到 Azure 资源管理器资源组。 Azure 门户中的资源边栏选项卡列出了所有已部署的资源，在部署和删除资源时提供始终最新的清单。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 控制措施 CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>信息系统组件清单 | 自动检测未经授权的组件

**CM-8 (3).a** 组织采用自动化机制[赋值：组织定义的频率]检测信息系统中未经授权的硬件、软件和固件组件。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用自动化机制检测客户部署的资源中未经授权的软件。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 控制措施 CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>信息系统组件清单 | 自动检测未经授权的组件

**CM-8 (3).b** 当检测到未经授权的组件时，组织采取以下操作：[选择（一个或多个）：禁用这些组件的网络访问；隔离该组件；通知[赋值：组织定义的人员或角色]]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在检测到未经授权的软件时采取操作。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 控制措施 CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>信息系统组件清单 | 问责制信息

**CM-8 (4)** 组织在信息系统中添加组件清单信息，根据[选择（一个或多个）：姓名；职位；角色]标识管理这些组件的责任人。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图将所有资源部署到 Azure 资源管理器资源组。 Azure 资源标记是用于对资源进行分类的键/值对，可用于问责和/或管理用途。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 控制措施 CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>信息系统组件清单 | 没有重复的组件核算

**CM-8 (5)** 组织验证信息系统授权范围内的所有组件没有在其他信息系统组件清单中重复。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图将所有资源部署到 Azure 资源管理器资源组。 Azure 资源管理器提供已部署资源的始终最新列表。 由此解决方案部署的资源会被赋予一个与系统边界相关的特定资源标记。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800-53 控制措施 CM-9.a

#### <a name="configuration-management-plan"></a>配置管理计划

**CM-9.a** 组织制定、记录和实施用于信息系统的配置管理计划，以解决角色、职责和配置管理流程和过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责制定、记录和实施用于客户部署的资源的配置管理计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800-53 控制措施 CM-9.b

#### <a name="configuration-management-plan"></a>配置管理计划

**CM-9.b** 组织制定、记录和实施用于信息系统的配置管理计划，以建立一个在整个系统开发生命周期中标识配置项，并管理该配置项的配置的流程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责制定、记录和实施用于客户部署的资源的配置管理计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 控制措施 CM-9.c

#### <a name="configuration-management-plan"></a>配置管理计划

**CM-9.c** 组织制定、记录和实施用于信息系统的配置管理计划，为信息系统定义配置项，并将该配置项置于配置管理之下。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责制定、记录和实施用于客户部署的资源的配置管理计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800-53 控制措施 CM-9.d

#### <a name="configuration-management-plan"></a>配置管理计划

**CM-9.d** 组织制定、记录和实施用于信息系统的配置管理计划，以保护配置管理计划免受未经授权的泄漏和修改。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责制定、记录和实施用于客户部署的资源的配置管理计划。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800-53 控制措施 CM-10.a

#### <a name="software-usage-restrictions"></a>软件使用限制

**CM-10.a** 组织根据合同协议和版权法使用软件和相关文档。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 为由此蓝图部署的资源提供了 Windows 和 SQL Server 许可证。 这是 Azure 的内置功能。 具有现有软件许可协议的组织可以考虑部署备用许可证模型。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800-53 控制措施 CM-10.b

#### <a name="software-usage-restrictions"></a>软件使用限制

**CM-10.b** 组织跟踪受大量许可证保护的软件和相关文档的使用，以便控制复制和分发。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 为由此蓝图部署的资源提供了 Windows 和 SQL Server 许可证。 用户不需要单独跟踪许可证的使用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800-53 控制措施 CM-10.c

#### <a name="software-usage-restrictions"></a>软件使用限制

**CM-10.c** 组织控制和记录对等文件共享技术的使用，以确保此功能不被用于对版权作品未经授权的分发、显示、使用或复制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图没有部署对等文件共享功能。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 控制措施 CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>软件使用限制 | 开放源代码软件

**CM-10 (1)** 组织对开放源代码软件的使用建立了以下限制：[赋值：组织定义的限制]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级配置管理策略可能会限制对开放源代码软件的使用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800-53 控制措施 CM-11.a

#### <a name="user-installed-software"></a>用户安装的软件

**CM-11.a** 组织建立了用于管理用户软件安装的[赋值：组织定义的策略]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责建立用于管理用户在客户部署的资源上安装软件的策略。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800-53 控制措施 CM-11.b

#### <a name="user-installed-software"></a>用户安装的软件

**CM-11.b** 组织通过[赋值：组织定义的方法]强制执行软件安装策略。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责强制执行软件安装策略。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800-53 控制措施 CM-11.c

#### <a name="user-installed-software"></a>用户安装的软件

**CM-11.c** 组织以[赋值：组织定义的频率]监视策略的符合性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用 CM-11.a 中定义的策略，监视客户部署资源的符合性。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 控制措施 CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>用户安装的软件 | 未经授权的安装的警报

**CM-11 (1)** 当检测到未经授权的软件安装时，信息系统会向[赋值：组织定义的人员或角色]发出警报。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在检测到未经授权软件安装时提供警报。 |
| **提供商 (Microsoft Azure)** | 不适用 |

