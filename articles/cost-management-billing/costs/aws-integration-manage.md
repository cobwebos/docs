---
title: 在 Azure 成本管理中管理 AWS 成本和使用情况
description: 本文可帮助你了解如何在成本管理中使用成本分析和预算来管理 AWS 成本和使用情况。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: a7520d7b3bd46aa9151c68598574f9cbec84d6fc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988612"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>在 Azure 中管理 AWS 成本和使用情况

为 Azure 成本管理设置并配置 AWS 成本和使用情况报表集成后，便可以开始管理 AWS 成本和使用情况。 本文可帮助你了解如何在成本管理中使用成本分析和预算来管理 AWS 成本和使用情况。

如果尚未配置集成，请参阅[设置和配置 AWS 使用情况报表集成](aws-integration-set-up-configure.md)。

 准备工作：如果不熟悉成本分析，请参阅[通过成本分析了解和分析成本](quick-acm-cost-analysis.md)快速入门。 如果不熟悉 Azure 中的预算，请参阅[创建并管理 Azure 预算](tutorial-acm-create-budgets.md)教程。

## <a name="view-aws-costs-in-cost-analysis"></a>在成本分析中查看 AWS 成本

AWS 成本可在以下范围内用于成本分析：

- 管理组下的 AWS 关联帐户
- AWS 关联帐户成本
- AWS 合并帐户成本

后续部分介绍如何使用范围来查看每个范围的成本和使用情况数据。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>查看管理组下的 AWS 关联帐户

使用管理组范围查看成本是查看来自不同订阅和关联帐户的聚合成本的唯一方法。 使用管理组可提供跨云视图。

在成本分析中打开范围选取器，选择包含 AWS 关联帐户的管理组。 下面是 Azure 门户中的示例图像：

![“选择范围”视图的示例](./media/aws-integration-manage/select-scope01.png)



下面是一个示例，显示了成本分析中按提供者（Azure 和 AWS）分组的管理组成本。

![示例，显示成本分析中一个季度的 Azure 和 AWS 成本](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>查看 AWS 关联帐户成本

若要查看 AWS 关联帐户成本，请打开范围选取器并选择 AWS 关联帐户。 请注意，关联帐户与 AWS 连接器中定义的管理组相关联。

下面是一个示例，演示如何选择 AWS 关联帐户范围。

![“选择范围”视图的示例](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>查看 AWS 合并帐户成本

若要查看 AWS 合并帐户成本，请打开范围选取器并选择 AWS 合并帐户。 下面是一个示例，演示如何选择 AWS 合并帐户范围。

![“选择范围”视图的示例](./media/aws-integration-manage/select-scope03.png)



此范围提供与 AWS 合并帐户关联的所有 AWS 关联帐户的聚合视图。 下面是一个示例，显示了按服务名称分组的 AWS 合并帐户成本。

![示例，显示成本分析中的 AWS 合并成本](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>可用于筛选和分组的维度

下表描述了可以在成本分析中用于分组和筛选的维度。

| 维度 | Amazon CUR 标头 | 作用域 | 注释 |
| --- | --- | --- | --- |
| 可用性区域 | lineitem/AvailabilityZone | All |   |
| 位置 | 产品/区域 | All |   |
| 计量 |   | All |   |
| 计量类别 | lineItem/ProductCode | All |   |
| 计量子类别 | lineitem/UsageType | All |   |
| Operation | lineItem/Operation | All |   |
| 资源 | lineItem/ResourceId | All |   |
| 资源类型 | product/instanceType | All | 如果 product/instanceType 为 null，则使用 lineItem/UsageType。 |
| ResourceGuid | 空值 | All | Azure 计量 GUID。 |
| Service name | product/ProductName | All | 如果 product/ProductName 为 null，则使用 lineItem/ProductCode。 |
| 服务层 |   |   |   |
| 订阅 ID | lineItem/UsageAccountId | 合并帐户和管理组 |   |
| 订阅名称 | 空值 | 合并帐户和管理组 | 帐户名称通过 AWS 组织 API 收集。 |
| 标记 | resourceTags/\* | All | 将从用户定义的标记中删除 _user:_ 前缀，以允许跨云标记。 _aws:_ 前缀保持不变。 |
| 计费帐户 ID | bill/PayerAccountId | 管理组 |   |
| 计费帐户名称 | 空值 | 管理组 | 帐户名称通过 AWS 组织 API 收集。 |
| 提供程序 | 空值 | 管理组 | AWS 或 Azure。 |

## <a name="set-budgets-on-aws-scopes"></a>设置基于 AWS 范围的预算

通过预算在组织中以前摄方式管理成本和推行责任制。 预算的设置基于 AWS 合并帐户和 AWS 关联帐户范围。 下面是在成本管理中显示的 AWS 合并帐户的预算示例：

![示例，显示 AWS 合并帐户的预算](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS 数据收集过程

设置 AWS 连接器后，数据收集和发现过程就会启动。 收集所有使用情况数据可能需要数小时。 持续时间取决于：

- 处理 AWS S3 Bucket 中的 CUR 文件所需的时间。
- 创建 AWS 合并帐户和 AWS 关联帐户范围所需的时间。
- AWS 在 S3 Bucket 中写入成本和使用情况报表文件的时间和频率

## <a name="aws-integration-pricing"></a>AWS 集成定价

每个 AWS 连接器可以免费试用 90 天。 公共预览版不收费。

标价为 AWS 每月成本的 1%。 每个月都会根据上个月的开票成本收费。

访问 AWS API 可能会产生额外成本。

## <a name="aws-integration-limitations"></a>AWS 集成限制

- 成本管理不支持包含多种货币类型的成本报表。 如果选择有多种货币的范围，则会显示一条错误消息。
- 云连接器不支持 AWS GovCloud（美国）、AWS Gov 或 AWS 中国。
- 成本管理仅显示  AWS 使用成本。 目前尚不支持税款、支持、退款、RI、额度或任何其他的费用类型。

## <a name="troubleshooting-aws-integration"></a>排查 AWS 集成问题

请使用以下故障排除信息来解决常见问题。

### <a name="no-permission-to-aws-linked-accounts"></a>没有访问 AWS 关联帐户的权限

**错误代码：** 未授权 

可以通过两种方式获取访问 AWS 关联帐户成本的权限：

- 获取对包含 AWS 关联帐户的管理组的访问权限。
- 让某人授予你访问 AWS 关联帐户的权限。

默认情况下，AWS 连接器创建者是连接器创建的所有对象的所有者。 其中包括 AWS 合并帐户和 AWS 关联帐户。

为了能够验证连接器设置，你至少需要一个参与者角色。读取者无法验证连接器设置

### <a name="collection-failed-with-assumerole"></a>使用 AssumeRole 进行收集失败

**错误代码：** _FailedToAssumeRole_

此错误表示成本管理无法调用 AWS AssumeRole API。 出现此问题的原因可能是角色定义有问题。 验证是否符合下列条件：

- 外部 ID 与角色定义和连接器定义中的相同。
- 角色类型设置为“属于你或第三方的其他 AWS 帐户”。 
- “要求 MFA”选项已取消选中。 
- AWS 角色中受信任的 AWS 帐户为 _432263259397_。

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>收集失败，出现“拒绝访问”错误 - CUR 报表定义

**错误代码：** _AccessDeniedReportDefinitions_

此错误表示成本管理无法查看成本和使用情况报表定义。 此权限用来验证 Azure 成本管理是否按预期定义了 CUR。 请参阅[在 AWS 中创建成本和使用情况报表](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)。

### <a name="collection-failed-with-access-denied---list-reports"></a>收集失败，出现“拒绝访问”错误 - 列出报表

**错误代码：** _AccessDeniedListReports_

此错误表示成本管理无法列出 CUR 所在的 S3 Bucket 中的对象。 AWS IAM 策略要求一项基于 Bucket 和 Bucket 中对象的权限。 请参阅[在 AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)。

### <a name="collection-failed-with-access-denied---download-report"></a>收集失败，出现“拒绝访问”错误 - 下载报表

**错误代码：** _AccessDeniedDownloadReport_

此错误表示成本管理无法访问和下载 Amazon S3 Bucket 中存储的 CUR 文件。 请确保附加到角色的 AWS JSON 策略与[在 AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)部分底部显示的示例类似。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>收集失败，因为我们找不到成本和使用情况报表

**错误代码：** _FailedToFindReport_

此错误表示成本管理找不到连接器中定义的成本和使用情况报表。 请确保它未被删除，且附加到角色的 AWS JSON 策略与[在 AWS 中创建角色和策略](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)部分底部显示的示例类似。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>由于成本和使用情况报表定义不匹配，无法创建或验证连接器

**错误代码：** _ReportIsNotValid_

此错误与 AWS 成本和使用情况报表的定义相关，我们要求对此报表使用特定设置。请参阅[在 AWS 中 创建成本和使用情况报表](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)中的要求。

## <a name="next-steps"></a>后续步骤

- 如果尚未配置包含管理组的 Azure 环境，请参阅[管理组的初始设置](../../governance/management-groups/overview.md#initial-setup-of-management-groups)。
