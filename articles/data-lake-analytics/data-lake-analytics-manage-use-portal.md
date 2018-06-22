---
title: 使用 Azure 门户管理 Azure Data Lake Analytics
description: 本文介绍如何使用 Azure 门户管理 Data Lake Analytics 帐户、数据源、用户和作业。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1ccd4dd6b8d4ee15b7d9f14e7436ccd87392121e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623700"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>使用 Azure 门户管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

本文介绍如何使用 Azure 门户管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>管理 Data Lake Analytics 帐户

### <a name="create-an-account"></a>创建帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“创建资源” > “智能 + 分析” > “Data Lake Analytics”。
3. 为以下项选择值： 
   1. 名称：Data Lake Analytics 帐户的名称。
   2. 订阅：用于帐户的 Azure 订阅。
   3. 资源组：要在其中创建帐户的 Azure 资源组。 
   4. 位置：Data Lake Analytics 帐户的 Azure 数据中心。 
   5. Data Lake Store：要用于 Data Lake Analytics 帐户的默认存储。 Azure Data Lake Store 帐户和 Data Lake Analytics 帐户必须位于同一位置。
4. 单击“创建”。 

### <a name="delete-a-data-lake-analytics-account"></a>删除 Data Lake Analytics 帐户

删除 Data Lake Analytics 帐户前，先删除其默认 Data Lake Store 帐户。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击“删除” 。
3. 键入帐户名称。
4. 单击“删除” 。

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>管理数据源

Data Lake Analytics 支持以下数据源：

* Data Lake Store
* Azure 存储

可使用数据资源管理器来浏览数据源和执行基本文件管理操作。 

### <a name="add-a-data-source"></a>添加数据源

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击“数据源”。
3. 单击“添加数据源”。
    
   * 若要添加 Data Lake Store 帐户，需要帐户名称和此帐户的访问权限才能对其进行查询。
   * 若要添加 Azure Blob 存储，需要存储帐户和帐户密钥。 若要找到它们，请在门户中转到存储帐户。

## <a name="set-up-firewall-rules"></a>设置防火墙规则

可使用 Data Lake Analytics 进一步在网络级别锁定访问 Data Lake Analytics 帐户的权限。 可启用防火墙、指定 IP 地址或定义受信任客户端的 IP 地址范围。 启用这些度量值后，只有具有定义范围内的 IP 地址的客户端才可连接到 Azure Data Lake Store。

如果其他 Azure 服务（如 Azure 数据工厂或 VM）连接到 Data Lake Analytics 帐户，请确保将“允许 Azure 服务”切换为“启用”。 

### <a name="set-up-a-firewall-rule"></a>设置防火墙规则。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 在左侧菜单中，单击“防火墙”。

## <a name="add-a-new-user"></a>添加新用户

可以使用“添加用户向导”轻松预配新的 Data Lake 用户。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 在左侧的“入门”下，单击“添加用户向导”。
3. 选择一个用户，然后单击“选择”。
4. 选择一个角色，然后单击“选择”。 若要设置要使用 Azure Data Lake 的新的开发人员，请选择“Data Lake Analytics 开发人员”角色。
5. 选择 U-SQL 数据库的访问控制列表 (ACL)。 如果对所选内容满意，请单击“选择”。
6. 选择文件的 ACL。 对于默认存储，不要更改根文件夹“/”和 /system 文件夹的 ACL。 单击“选择”。
7. 查看所选的所有更改，然后单击“运行”。
8. 完成向导后，单击“完成”。

## <a name="manage-role-based-access-control"></a>管理基于角色的访问控制

与其他 Azure 服务一样，可使用基于角色的访问控制 (RBAC) 来控制用户与服务交互的方式。

标准 RBAC 角色具有以下功能：
* 所有者：可提交作业、监视作业、从任何用户取消作业以及配置帐户。
* 参与者：可提交作业、监视作业、从任何用户取消作业以及配置帐户。
* 读者：可监视作业。

使用 Data Lake Analytics 开发人员角色使 U-SQL 开发人员能够使用 Data Lake Analytics 服务。 可使用 Data Lake Analytics 开发人员角色执行以下操作：
* 提交作业。
* 监视作业状态以及任何用户提交的作业进度。
* 查看任何用户提交的作业中的 U-SQL 脚本。
* 只取消自己的作业。

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>将用户或安全组添加到 Data Lake Analytics 帐户

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击“访问控制 (IAM)” > “添加”。
3. 选择角色。
4. 添加用户。
5. 单击“确定”。

>[!NOTE]
>如果用户或安全组需要提交作业，还需要对存储帐户的权限。 有关详细信息，请参阅[保护 Data Lake Store 中存储的数据](../data-lake-store/data-lake-store-secure-data.md)。
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>管理作业

### <a name="submit-a-job"></a>提交作业

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。

2. 单击“新建作业”。 对每个作业配置以下各项：

    1. 作业名称：作业的名称。
    2. 优先级：数字越小优先级越高。 如果队列有两个作业，将先运行优先级值低的作业。
    3. 并行：为此作业保留的最大计算进程数。

3. 单击“提交”。

### <a name="monitor-jobs"></a>监视作业

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击“查看所有作业”。 将显示帐户中所有活动作业和最近已完成作业的列表。
3. 根据需要单击“筛选器”，帮助按“时间范围”、“作业名称”和“作者”值查找作业。 

### <a name="monitoring-pipeline-jobs"></a>监视管道作业
管道中包含的作业通常按顺序一起工作，以完成特定方案。 例如，可使用一个管道来清理、提取、转换和聚合使用情况，获取客户见解。 提交作业时，使用“Pipeline”属性标识管道作业。 使用 ADF V2 计划的作业会自动填充此属性。 

查看管道中包含的 U-SQL 作业的列表： 

1. 在 Azure 门户中，转到 Data Lake Analytics 帐户。
2. 单击“作业见解”。 默认显示“所有作业”选项卡，即显示正在运行的作业、排队的作业以及已结束作业的列表。
3. 单击“管道作业”选项卡。管道作业列表随每个管道的聚合统计信息一起显示。

### <a name="monitoring-recurring-jobs"></a>监视定期作业
定期作业是指具有相同的业务逻辑，但每次运行时均使用不同输入数据的作业。 理想情况下，定期作业应始终成功，并具有相对稳定的执行时间；监视这些行为有助于确保作业处于正常状态。 定期作业是使用“Recurrence”属性标识的。 使用 ADF V2 计划的作业会自动填充此属性。

查看定期 U-SQL 作业的列表： 

1. 在 Azure 门户中，转到 Data Lake Analytics 帐户。
2. 单击“作业见解”。 默认显示“所有作业”选项卡，即显示正在运行的作业、排队的作业以及已结束作业的列表。
3. 单击“定期作业”选项卡。定期作业列表随每个定期作业的聚合统计信息一起显示。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
* [使用策略管理 Azure Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-policies)
