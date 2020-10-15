---
title: 通过 Azure 资源转移器在区域之间移动 Azure SQL 资源
description: 了解如何使用 Azure 资源转移器将 Azure SQL 资源移动到另一个区域
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e3e2c9aa42ff3189e90f57d7c6e92b2a71f46639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061592"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>教程：将 Azure SQL 数据库资源移动到其他区域

在本教程中，了解如何使用 [Azure 资源转移器](overview.md)将 Azure SQL 数据库和弹性池移动到不同的 Azure 区域。

> [!NOTE]
> Azure 资源转移器目前提供预览版。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 查看先决条件和要求。
> * 选择要移动的资源。
> * 解决资源依赖项。
> * 准备 SQL Server 并将其移动到目标区域。
> * 准备并移动数据库和弹性池。
> * 确定是放弃还是提交移动。 
> * 移动后，可以选择删除源区域中的资源。 

> [!NOTE]
> 教程显示尝试方案的最快路径，并使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。 然后登录到 [Azure 门户](https://portal.azure.com)。

## <a name="prerequisites"></a>先决条件

-  检查以确定你对包含要移动的资源的订阅具有“所有者”访问权限。
    - 首次为 Azure 订阅中的特定源和目标对添加资源时，资源转移器将创建受订阅信任的[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)（以前称为托管服务标识 (MSI)）。
    - 若要创建标识，并为其分配所需的角色（来源订阅中的参与者或用户访问管理员），用于添加资源的帐户需要对订阅的“所有者”权限。 [详细了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
- 订阅需要足够的配额来创建要在目标区域中移动的资源。 如果没有配额，请[请求其他限制](/azure/azure-resource-manager/management/azure-subscription-service-limits)。
- 验证与要将资源移动到的目标区域关联的定价和费用。 请使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)来帮助你。
    

## <a name="check-sql-requirements"></a>查看 SQL 需求

1. [查看](support-matrix-move-region-sql.md)移动到其他区域时支持哪些数据库/弹性池功能。
2. 在目标区域中，为每个源服务器创建目标服务器。 [了解详细信息](/azure/azure-sql/database/active-geo-replication-security-configure#how-to-configure-logins-and-users)。
4. 如果数据库使用透明数据加密 (TDE) 进行加密，并且你在 Azure Key Vault 中使用自己的加密密钥，请[了解如何](../key-vault/general/move-region.md)将密钥保管库移动到其他区域。
5. 如果启用了 SQL 数据同步，则支持移动成员数据库。 移动后，需要将 SQL 数据同步设置为新的目标数据库。
6. 在移动前删除高级数据安全设置。 移动后，在目标区域的 SQL Server 级别[配置设置](/azure/sql-database/sql-database-advanced-data-security)。
7. 如果启用审核，策略在移动后将重置为默认值。 移动后，请再次[设置审核](/azure/sql-database/sql-database-auditing)。
7. 源数据库的备份保留策略将转移到目标数据库。 [详细了解](/azure/sql-database/sql-database-long-term-backup-retention-configure )如何在移动后修改设置。
8. 在移动之前删除服务器级防火墙规则。 在移动过程中，数据库级防火墙规则将从源服务器复制到目标服务器。 移动后，在目标区域中为 SQL Server [设置防火墙规则](/azure/sql-database/sql-database-server-level-firewall-rule)。
9. 在移动前删除自动优化设置。 移动后再次[设置自动优化](/azure/sql-database/sql-database-automatic-tuning-enable)。
10. 在移动前删除数据库警报设置。 移动后[重置](/azure/sql-database/sql-database-insights-alerts-portal)。
    
## <a name="select-resources"></a>选择资源

选择要移动的资源。

- 可以在所选源区域中的任何资源组中选择任何受支持的资源类型。
- 将资源移动到与源区域相同的订阅中的目标区域。 如果要更改订阅，可以在移动资源后进行更改。

1. 在 Azure 门户中，搜索“资源转移器”。 然后，在“服务”下，选择“Azure 资源转移器” 。

     ![Azure 门户中资源转移器的搜索结果](./media/tutorial-move-region-sql/search.png)

2. 在“概述”中，单击“入门” 。

    ![用于添加要移动到其他区域的资源的按钮](./media/tutorial-move-region-sql/get-started.png)

3. 在“移动资源” > “源 + 目标”中，选择来源订阅和区域 。
4. 在“目标”中，选择要将资源移动到的区域。 然后单击“下一步”  。

    ![用于选择源区域和目标区域的页面](./media/tutorial-move-region-sql/source-target.png)

6. 在“要移动的资源”中，单击“选择资源” 。
7. 在“选择资源”中，选择资源。 只能添加支持移动的资源。 然后单击“完成”。

    ![用于选择要移动的 SQL 资源的页面](./media/tutorial-move-region-sql/select-resources.png)

8. 在“要移动的资源”中，单击“下一步” 。

9. 在“查看 + 添加”中，查看源和目标设置。 确认你了解有关移动的元数据将存储在元数据区域中为此目的创建的资源组中。


    ![用于检查设置并继续移动的页面](./media/tutorial-move-region-sql/review.png)

10. 单击“继续”，开始添加资源。
11. 添加过程成功完成后，单击通知图标中的“添加要移动的资源”。
12. 单击通知后，查看“跨区域”页上的资源。


> [!NOTE]
> 
> - SQL Server 现在处于“手动分配挂起”状态。
> - 已添加的其他资源处于“准备挂起”状态。
> - 如果要从移动集合中删除资源，所用的方法取决于你在移动过程中的进度。 [了解详细信息](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>解决依赖项问题


1. 在“跨区域”中，如果资源在“问题”列中显示“验证依赖项”消息，请单击“验证依赖项”按钮 。 验证过程开始。
2. 如果找到依赖项，请单击“添加依赖项”。

    ![添加依赖项的按钮](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. 在“添加依赖项”中，选择从属资源 >”添加依赖项” 。 在通知中监视进度。

4. 如果需要，添加其他依赖项，并再次验证依赖项。 

5. 在“跨区域”页上，验证资源现在是否处于“准备挂起”状态，且没有任何问题。

    ![页面显示处于“准备挂起”状态的资源](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>移动 SQL Server

在目标区域中分配目标 SQL Server，然后提交移动。

### <a name="assign-a-target-sql-server"></a>分配目标 SQL Server

1. 在“跨区域”中，对于 SQL Server 资源，在“目标配置”列中单击“未分配的资源”  。
2. 选择目标区域中的现有 SQL Server 资源。 
    
    ![显示 SQL Server 状态设置为“提交移动挂起”的项](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> 源 SQL Server 状态将更改为“提交移动挂起”。 

### <a name="commit-the-sql-server-move"></a>提交 SQL Server 移动

1. 在“跨区域”中，选择 SQL Server，然后单击“提交移动” 。
2. 在“提交资源”中，单击“提交” 。

    ![用于提交 SQL Server 移动的页面](./media/tutorial-move-region-sql/commit-sql-server.png)

3. 跟踪通知栏中的移动进度。

> [!NOTE]
> 提交后，SQL Server 现在处于“删除源挂起”状态。


## <a name="prepare-resources-to-move"></a>准备要移动的资源

移动源 SQL Server 后，可以准备移动其他资源。

## <a name="prepare-an-elastic-pool"></a>准备缩放弹性池

1. 在“跨区域”中，选择源弹性池（演练中的 demo-test1-elasticpool），然后单击“准备” 。

    ![准备资源的按钮](./media/tutorial-move-region-sql/prepare-elastic.png)

2. 在“准备资源”中，单击“准备” 。
3. 当通知显示准备过程成功时，单击“刷新”。

> [!NOTE]
> 弹性池现在处于“启动移动挂起”状态。

## <a name="prepare-a-single-database"></a>准备单一数据库

1. 在“跨区域”中，选择（不在弹性池中的）单个数据库，然后单击“准备” 。

    ![准备选定的资源的按钮](./media/tutorial-move-region-sql/prepare-db.png)

2. 在“准备资源”中，单击“准备” 。
3. 当通知显示准备过程成功时，单击“刷新”。

> [!NOTE]
> 该数据库现在处于“启动移动挂起”状态，并且已在目标区域中创建。


## <a name="move-the-pool-and-prepare-pool-databases"></a>移动池并准备池数据库

若要在弹性池中准备数据库，弹性池必须处于“提交移动挂起”状态。 若要移动到此状态，请启动池的移动。

#### <a name="initiate-move---elastic-pool"></a>启动移动 - 弹性池

1. 在“跨区域”中，选择源弹性池（演练中的 demo-test1-elasticpool），然后单击“启动移动” 。
2. 在“移动资源”中，单击“启动移动” 。

    
    ![用于启动弹性池移动的按钮](./media/tutorial-move-region-sql/initiate-elastic.png)

1. 跟踪通知栏中的移动进度。
1. 当通知显示移动成功时，单击“刷新”。

> [!NOTE]
> 弹性池现在处于“提交移动挂起”状态。

#### <a name="prepare-database"></a>准备数据库

1. 在“跨区域”中，选择数据库（在我们演练中的 demo-test2-sqldb），然后单击“准备” 。
2. 在“准备资源”中，单击“准备” 。

    ![用于在弹性池中准备数据库的按钮](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

准备期间，将在目标区域中创建目标数据库，并开始复制数据。 准备后，数据库处于“启动移动挂起”状态。 

![用于在弹性池中准备所选数据库的按钮](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>移动数据库

开始移动数据库。
1. 在“跨区域”中，选择状态“启动移动挂起”的资源 。 然后单击“启动移动”。
2. 在“移动资源”中，单击“启动移动” 。

    ![用于启动移动的页面](./media/tutorial-move-region-sql/initiate-move.png)

3. 跟踪通知栏中的移动进度。

> [!NOTE]
> 数据库现在处于“提交移动挂起”状态。


## <a name="discard-or-commit"></a>放弃还是提交？

初始移动后，你可以决定是提交移动，还是放弃它。 

- **放弃**：如果要测试，并且不想实际移动源资源，则可能需要放弃移动。 放弃移动会将资源返回到“启动移动挂起”状态。
- **提交**：提交即完成移动到目标区域的操作。 提交后，源资源将处于“删除源挂起”状态，你可以决定是否要删除它。


## <a name="discard-the-move"></a>放弃移动 

你可以按如下所示放弃移动：

1. 在“跨区域”中，选择“提交移动挂起”状态的资源，然后单击“放弃移动”  。
2. 在“放弃移动”中，单击“放弃” 。
3. 跟踪通知栏中的移动进度。


> [!NOTE]
> - 放弃资源后，它们处于“启动移动挂起”状态。
> - 如果只有一个弹性池，则放弃进度，并删除在目标区域中创建的弹性池。
> - 如果存在具有处于“提交移动挂起”状态的关联数据库的弹性池，则不能放弃弹性池。
> - 如果放弃 SQL 数据库，不会删除目标区域资源。 

如果要在放弃后再次启动移动，请选择 SQL 数据库或弹性池，然后再次启动移动。


## <a name="commit-the-move"></a>提交移动

完成移动数据库和弹性池的过程，如下所示：


1. 查看 SQL Server 是否为“删除源挂起”状态。
2. 在提交之前，将数据库连接字符串更新到目标区域。
3. 在“跨区域”中，选择 SQL 资源，然后单击“提交移动” 。
4. 在“提交资源”中，单击“提交” 。

    ![提交移动](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. 跟踪通知栏中的提交进度。


> [!NOTE]
> 在提交过程中，SQL 数据库会出现停机。
> 已提交的数据库和弹性池现在处于“删除源挂起”状态。
> 提交后，更新目标数据库上与数据库相关的设置，包括防火墙规则、策略和警报。


## <a name="delete-source-resources-after-commit"></a>提交后删除源资源

移动后，可以选择删除源区域中的资源。 

1. 在“跨区域”中，单击要删除的每个源资源的名称。
2. 在每个资源的属性页中，选择“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 将 Azure SQL 数据库移动到其他 Azure 区域。
> * 将 Azure SQL 弹性池移动到其他区域。

现在，尝试将 Azure VM 移动到其他区域。

> [!div class="nextstepaction"]
> [移动 Azure VM](./tutorial-move-region-virtual-machines.md)
