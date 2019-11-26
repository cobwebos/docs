---
title: 管理 Azure 成本管理中的 AWS 成本和使用情况
description: 本文将帮助你了解如何在成本管理中使用成本分析和预算来管理你的 AWS 成本和使用情况。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 04f607d7f592112013bcc6cf4cbd61d42a8116a8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230163"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>管理 Azure 中的 AWS 成本和使用情况

为 Azure 成本管理设置并配置 AWS 成本和使用情况报告集成后，便可以开始管理 AWS 成本和使用情况。 本文将帮助你了解如何在成本管理中使用成本分析和预算来管理你的 AWS 成本和使用情况。

如果尚未配置集成，请参阅[设置和配置 AWS 使用情况报表集成](aws-integration-set-up-configure.md)。

_开始之前_：如果不熟悉成本分析，请参阅[利用成本分析快速入门探索和分析成本](quick-acm-cost-analysis.md)。 如果不熟悉 Azure 中的预算，请参阅[创建和管理 azure 预算](tutorial-acm-create-budgets.md)教程。

## <a name="view-aws-costs-in-cost-analysis"></a>查看成本分析中的 AWS 成本

AWS 成本在以下范围内可用于成本分析：

- 管理组下的 AWS 链接帐户
- AWS 链接帐户成本
- AWS 合并帐户成本

下一部分介绍如何使用作用域，以便查看每个作用域的成本和使用情况数据。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>查看管理组下的 AWS 链接帐户

使用管理组范围查看成本是查看来自不同订阅和链接帐户的聚合成本的唯一方法。 使用管理组可提供跨云视图。

在 "成本分析" 中，打开 "作用域选取器" 并选择包含 AWS 链接帐户的管理组。 下面是 Azure 门户中的示例图像：

!["选择作用域" 视图的示例](./media/aws-integration-manage/select-scope01.png)



下面是一个示例，显示了按提供程序（Azure 和 AWS）分组的成本分析中的管理组成本。

![显示成本分析中每个季度的 Azure 和 AWS 成本的示例](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>查看 AWS 链接帐户成本

若要查看 AWS 链接帐户成本，请打开作用域选取器并选择 "AWS" 链接帐户。 请注意，链接的帐户与 AWS 连接器中定义的管理组相关联。

下面是一个示例，演示如何选择 AWS 链接的帐户范围。

!["选择作用域" 视图的示例](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>查看 AWS 合并帐户成本

若要查看 AWS 合并帐户成本，请打开作用域选取器并选择 "AWS" 合并帐户。 下面是一个示例，演示如何选择 AWS 合并帐户范围。

!["选择作用域" 视图的示例](./media/aws-integration-manage/select-scope03.png)



此作用域提供与 AWS 合并帐户关联的所有 AWS 链接帐户的聚合视图。 下面是一个示例，显示了按服务名称分组的 AWS 合并帐户的成本。

![显示成本分析中 AWS 合并成本的示例](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>可用于筛选和分组的维度

下表描述了在成本分析中可用于分组和筛选的维度。

| 维度 | Amazon 当前标头 | 作用域 | 注释 |
| --- | --- | --- | --- |
| 可用性区域 | lineitem/AvailabilityZone | All |   |
| 位置 | 产品/区域 | All |   |
| 计量 |   | All |   |
| 计量类别 | lineItem/ProductCode | All |   |
| 计量子类别 | lineitem/UsageType | All |   |
| Operation | lineItem/Operation | All |   |
| Resource | lineItem/ResourceId | All |   |
| 资源类型 | product/instanceType | All | 如果 product/instanceType 为 null，则使用 lineItem/UsageType。 |
| ResourceGuid | 不适用 | All | Azure 计量器 GUID。 |
| 服务名称 | product/ProductName | All | 如果 product/ProductName 为 null，则使用 lineItem/ProductCode。 |
| 服务层 |   |   |   |
| 订阅 ID | lineItem/UsageAccountId | 合并的帐户和管理组 |   |
| 订阅名称 | 不适用 | 合并的帐户和管理组 | 帐户名称是使用 AWS 组织 API 收集的。 |
| 标记 | resourceTags/\* | All | 将从用户定义的标记中删除_用户：_ 前缀以允许跨云标记。 _Aws：_ 前缀保持不变。 |
| 计费帐户 ID | bill/PayerAccountId | 管理组 |   |
| 计费帐户名称 | 不适用 | 管理组 | 帐户名称是使用 AWS 组织 API 收集的。 |
| 提供程序 | 不适用 | 管理组 | AWS 或 Azure。 |

## <a name="set-budgets-on-aws-scopes"></a>在 AWS 范围上设置预算

使用预算在你的组织中主动管理成本和促进责任。 预算在 AWS 合并帐户和 AWS 链接帐户范围内设置。 下面是 "成本管理" 中显示的 AWS 合并帐户的预算示例：

![显示 AWS 合并帐户预算的示例](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 数据收集过程

设置 AWS 连接器后，将启动数据收集和发现进程。 收集所有使用数据可能需要几个小时。 持续时间取决于：

- 处理 AWS S3 存储桶中的当前文件所需的时间。
- 创建 AWS 合并帐户和 AWS 链接帐户范围所需的时间。
- AWS 在 S3 存储桶中写入成本和使用情况报表文件的时间和频率

## <a name="aws-integration-pricing"></a>AWS 集成定价

每个 AWS 连接器获取90免费试用期。 公共预览期间，不收取任何费用。

定价为 AWS 每月费用的1%。 每个月都会根据上个月的开票成本收费。

访问 AWS Api 可能会产生额外的成本。

## <a name="aws-integration-limitations"></a>AWS 集成限制

- 成本管理不支持包含多种货币类型的成本报表。 如果选择具有多个货币的作用域，将显示一条错误消息。
- 云连接器不支持 AWS GovCloud （US）、AWS Gov 或 AWS 中国。
- 成本管理只显示 AWS 的_使用成本_。 目前尚不支持税、支持、退款、RI、信用额度或其他任何费用类型。

## <a name="troubleshooting-aws-integration"></a>AWS 集成故障排除

使用以下故障排除信息来解决常见问题。

### <a name="no-permission-to-aws-linked-accounts"></a>无权 AWS 链接的帐户

**错误代码：** _未经授权_

可以通过两种方式获取访问 AWS 链接帐户成本的权限：

- 获取对具有 AWS 链接帐户的管理组的访问权限。
- 让用户授予你 AWS 链接帐户的权限。

默认情况下，AWS 连接器创建者是连接器创建的所有对象的所有者。 包括 AWS 合并帐户和 AWS 链接帐户。

为了能够验证连接器设置，你至少需要一个参与者角色，读取者无法验证连接器设置

### <a name="collection-failed-with-assumerole"></a>收集失败，出现 AssumeRole

**错误代码：** _FailedToAssumeRole_

此错误表示成本管理无法调用 AWS AssumeRole API。 出现此问题的原因可能是角色定义有问题。 验证是否满足以下条件：

- 外部 ID 与角色定义和连接器定义中的 ID 相同。
- 角色类型设置为**属于你或第三方的其他 AWS 帐户。**
- "**需要 MFA** " 选项已清除。
- AWS 角色中受信任的 AWS 帐户为_432263259397_。

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>收集失败，拒绝访问-当前报表定义

**错误代码：** _AccessDeniedReportDefinitions_ 

此错误表示成本管理无法查看成本和使用情况报告定义。 此权限用来验证 Azure 成本管理是否按预期定义了当前的。 请参阅[在 AWS 中创建成本和使用情况报表](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)。

### <a name="collection-failed-with-access-denied---list-reports"></a>收集失败，拒绝访问-列表报告

**错误代码：** _AccessDeniedListReports_ 

此错误表示成本管理无法列出当前所在的 S3 存储桶中的对象。 AWS IAM 策略要求对 bucket 和 bucket 中的对象具有权限。 请参阅[在 AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)。

### <a name="collection-failed-with-access-denied---download-report"></a>收集失败，拒绝访问-下载报表 

**错误代码：** _AccessDeniedDownloadReport_ 

此错误表示成本管理无法访问和下载 Amazon S3 存储桶中存储的当前文件。 确保附加到该角色的 AWS JSON 策略类似于在[AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)部分底部显示的示例。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>收集失败，因为找不到成本和使用情况报告

**错误代码：** _FailedToFindReport_

此错误表示成本管理找不到连接器中定义的成本和使用情况报告。 请确保它未被删除，并且附加到该角色的 AWS JSON 策略类似于在[AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)部分底部显示的示例。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>由于成本和使用情况报告定义不匹配，无法创建或验证连接器

**错误代码：** _ReportIsNotValid_

此错误与 AWS 成本和使用情况报告的定义有关，我们需要此报表的特定设置，请参阅在[AWS 中创建成本和使用情况报表中](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)的要求。

## <a name="next-steps"></a>后续步骤

- 如果尚未配置具有管理组的 Azure 环境，请参阅[管理组的初始设置](../governance/management-groups/overview.md#initial-setup-of-management-groups)。
