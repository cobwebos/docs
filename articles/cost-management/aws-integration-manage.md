---
title: 管理 AWS 成本和 Azure 成本管理中的使用情况
description: 本文可帮助你了解如何使用在成本管理中成本分析和预算，来管理你的 AWS 成本和使用情况。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870307"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>管理 AWS 成本和在 Azure 中的使用情况

已设置并配置 Azure 成本管理的 AWS 成本和使用情况报表集成后，你准备好开始管理你的 AWS 成本和使用情况。 本文可帮助你了解如何使用在成本管理中成本分析和预算，来管理你的 AWS 成本和使用情况。

如果你尚未配置集成，请参阅[设置并配置 AWS 使用情况报表集成](aws-integration-set-up-configure.md)。

_在开始之前_:如果已熟悉成本分析，请参阅[探索和分析使用成本分析成本](quick-acm-cost-analysis.md)快速入门。 并且，如果您熟悉在 Azure 中的预算，请参阅[创建和管理 Azure 预算](tutorial-acm-create-budgets.md)教程。

## <a name="view-aws-costs-in-cost-analysis"></a>成本分析视图 AWS 成本

在以下范围中的成本分析中提供了 AWS 成本：

- 管理组下的链接的 AWS 帐户
- 链接的 AWS 帐户成本
- 合并的 AWS 帐户成本

后续部分介绍如何使用作用域，以便看到为每个成本和使用情况数据。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>在管理组下查看链接的 AWS 帐户

通过使用管理组作用域查看成本是唯一的方法，以查看聚合来自不同的订阅的成本和链接帐户。 使用管理组提供跨云视图。

在成本分析中，打开作用域选择器并选择包含链接的 AWS 帐户的管理组。 下面是在 Azure 门户中的示例图像：

![选择作用域视图的示例](./media/aws-integration-manage/select-scope01.png)



下面是在成本分析中，按提供程序 （Azure 和 AWS） 分组显示管理组成本示例。

![显示有关每个季度中成本分析 Azure 和 AWS 成本示例](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>查看链接的 AWS 帐户成本

若要查看 AWS 链接帐户成本，打开作用域选取器，选择 AWS 链接帐户。 请注意，链接的帐户关联到的管理组，AWS 连接器中定义。

下面是示例显示了选择 AWS 链接帐户作用域。

![选择作用域视图的示例](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>视图 AWS 合并帐户成本

要查看 AWS 合并的帐户成本，打开作用域选取器，并选择 AWS 合并的帐户。 下面是示例显示了选择 AWS 合并帐户作用域。

![选择作用域视图的示例](./media/aws-integration-manage/select-scope03.png)



此作用域提供所有 AWS 的聚合的视图链接合并的 AWS 帐户与关联的帐户。 下面是一个示例，演示的 AWS 成本合并帐户，按服务名分组。

![显示 AWS 示例合并中成本分析成本](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>维度可用于筛选和分组

下表介绍可用于组和通过成本分析中的筛选器的维度。

| 维度 | Amazon CUR 标头 | 作用域 | 注释 |
| --- | --- | --- | --- |
| 可用性区域 | lineitem/AvailabilityZone | 全部 |   |
| Location | 产品/地区 | 全部 |   |
| 计量 |   | 全部 |   |
| 计量类别 | lineItem/ProductCode | 全部 |   |
| 计量子类别 | lineitem/UsageType | 全部 |   |
| Operation | lineItem/Operation | 全部 |   |
| Resource | lineItem/ResourceId | 全部 |   |
| 资源类型 | product/instanceType | 全部 | 如果产品/instanceType 为 null，则使用 lineItem/UsageType。 |
| ResourceGuid | 不适用 | 全部 | Azure 计量 GUID。 |
| 服务名称 | product/ProductName | 全部 | 如果产品/ProductName 为 null，则使用 lineItem/产品代码。 |
| 服务层 |   |   |   |
| 订阅 ID | lineItem/UsageAccountId | 合并的帐户和管理组 |   |
| 订阅名称 | 不适用 | 合并的帐户和管理组 | 帐户名称是使用 AWS 组织 API 收集的。 |
| 标记 | resourceTags/\* | 全部 | _用户：_ 前缀已从用户定义标记，以允许跨云的标记。 _Aws:_ 前缀将保持不变。 |
| 计费帐户 ID | bill/PayerAccountId | 管理组 |   |
| 计费帐户名称 | 不适用 | 管理组 | 帐户名称是使用 AWS 组织 API 收集的。 |
| 提供程序 | 不适用 | 管理组 | AWS 或 Azure。 |

## <a name="set-budgets-on-aws-scopes"></a>在 AWS 作用域上的组预算

使用预算来主动管理成本，并带来在组织中的责任。 预算设置合并的 AWS 帐户和 AWS 上链接的帐户作用域。 下面是在成本管理中所示的合并的 AWS 帐户预算的示例：

![示例显示预算的 AWS 合并帐户](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 数据收集过程

设置后 AWS 连接器，数据收集和发现进程启动。 可能需要几个小时才能收集所有使用情况数据。 持续时间取决于：

- 若要处理在 AWS S3 存储桶中的当前文件所需的时间。
- 若要创建合并的 AWS 帐户和 AWS 链接帐户作用域所需的时间。
- 时间和频率的 AWS S3 存储桶中编写的成本和使用情况报告文件

## <a name="aws-integration-pricing"></a>AWS 集成定价

每个 AWS 连接器获取 90 天免费试用版。 在公共预览期间不收费。

列表价格是你的 AWS 每月费用的 1%。 向你收取每月根据你在前一个月的开票成本。

访问 AWS Api 可能会导致附加成本。

## <a name="aws-integration-limitations"></a>AWS 集成限制

- 成本管理不支持包含多个货币类型的成本报表。 如果选择具有多种货币的作用域显示错误消息。
- AWS GovCloud （美国）、 AWS 政府或 AWS 中国，不支持云连接器。
- 成本的管理将显示 AWS_使用成本_仅。 税务，支持，退款，RI、 信用额度或任何其他类型尚不支持的费用。

## <a name="troubleshooting-aws-integration"></a>AWS 集成疑难解答

使用以下故障排除信息来解决常见的问题。

### <a name="no-permission-to-aws-linked-accounts"></a>AWS 链接的帐户没有权限

有两种方法以获取权限来访问链接的 AWS 帐户费用：

- 获取对具有 AWS 链接帐户的管理组的访问权限。
- 让人为您提供到链接的 AWS 帐户的权限。

默认情况下，AWS 连接器创建者是创建了连接器的所有对象的所有者。 包括 AWS 合并的帐户和 AWS 链接帐户。

### <a name="collection-failed-with-assumerole"></a>失败，出现 AssumeRole 集合

此错误意味着成本管理是无法调用 AWS AssumeRole API。 此问题可能是由角色定义的问题。 验证以下条件为真：

- 外部 ID 是与一个角色定义和连接器定义中相同。
- 角色类型设置为**另一个 AWS 帐户属于你或第三方。**
- **要求使用 MFA**清除选择。
- AWS 角色中受信任的 AWS 帐户是_432263259397_。

### <a name="collection-failed-with-access-denied"></a>失败，出现拒绝访问的集合

此错误消息意味着成本管理是无法访问存储在 Amazon S3 存储桶的当前文件。 请确保附加到角色的 AWS JSON 策略类似于在底部所示的示例[在 AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)部分。

### <a name="connector-error-with-failedtofindreport"></a>使用 FailedToFindReport 连接器错误

此错误意味着成本管理找不到连接器中定义的成本和使用情况报告。 请确保它不会被删除，附加到角色的 AWS JSON 策略类似于在底部所示的示例[在 AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)部分。

## <a name="next-steps"></a>后续步骤

- 如果你尚未使用管理组配置 Azure 环境，请参阅[初始的管理组设置](../governance/management-groups/index.md#initial-setup-of-management-groups)。
