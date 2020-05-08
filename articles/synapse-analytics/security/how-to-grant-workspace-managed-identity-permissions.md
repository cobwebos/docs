---
title: 如何向 Azure Synapse 工作区中的托管标识授予权限
description: 本文介绍如何在 Azure Synapse 工作区中配置托管标识的权限。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 1f0644c25d0047f774fe8f99efa34a33e10d7b2b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983289"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>向工作区托管标识授予权限（预览）

本文介绍如何向 Azure synapse 工作区中的托管标识授予权限。 权限进而允许访问工作区中的 SQL 池，并通过 Azure 门户 ADLS Gen2 存储帐户。

>[!NOTE]
>在此文档的剩余部分中，我们将此工作区托管标识称为托管标识。

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>向 SQL 池授予托管标识权限

托管标识授予对工作区中的 SQL 池的权限。 在授予权限的情况下，你可以安排执行与 SQL 池相关的活动的管道。 使用 Azure 门户创建 Azure Synapse 工作区时，可以授予对 SQL 池的托管标识控制权限。

创建 Azure Synapse 工作区时，请选择 "**安全 + 网络**"。 然后 **，选择 "对 SQL 池中的工作区托管标识授予控制"**。

![SQL 池上的 CONTROL 权限](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>向托管标识授予对 ADLS Gen2 存储帐户的权限

创建 Azure Synapse 工作区需要 ADLS Gen2 存储帐户。 若要在 Azure Synapse 工作区中成功启动 Spark 池，Azure Synapse 托管标识需要此存储帐户上的 "*存储 Blob 数据参与者*" 角色。 Azure Synapse 中的管道业务流程也可以从此角色获益。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>创建工作区时向托管标识授予权限

使用 Azure 门户创建 Azure Synapse 工作区后，Azure Synapse 将尝试向托管标识授予存储 Blob 数据参与者角色。 你将在 "**基本**信息" 选项卡中提供 ADLS Gen2 存储帐户的详细信息。

![工作区创建流中的 "基本信息" 选项卡](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

在 "**帐户名称**" 和 "**文件系统名称**" 中选择 ADLS Gen2 存储帐户和文件系统。

![提供 ADLS Gen2 存储帐户详细信息](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

如果工作区创建者也是 ADLS Gen2 存储帐户的**所有者**，则 Azure Synapse 会将*存储 Blob 数据参与者*角色分配给托管标识。 你将在输入的存储帐户详细信息下看到以下消息。

![成功的存储 Blob 数据参与者分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

如果工作区创建者不是 ADLS Gen2 存储帐户的所有者，则 Azure Synapse 不会将*存储 Blob 数据参与者*角色分配给托管标识。 显示在存储帐户详细信息下面的消息将通知工作区创建者他们没有足够的权限向托管标识授予*存储 Blob 数据参与者*角色。

![不成功的存储 Blob 数据参与者分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

作为消息状态，除非将*存储 Blob 数据参与者*分配给托管标识，否则无法创建 Spark 池。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>创建工作区后向托管标识授予权限

在工作区创建期间，如果未将*存储 Blob 数据参与者*分配给托管标识，则 ADLS Gen2 存储帐户的**所有者**将手动将该角色分配给该标识。 以下步骤将帮助你完成手动分配。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>步骤1：导航到 Azure 门户中的 ADLS Gen2 存储帐户

在 Azure 门户中，打开 ADLS Gen2 存储帐户，并从左侧导航栏中选择 "**概述**"。 只需将*存储 Blob 数据参与者*角色分配到容器或文件系统级别。 选择“容器”****。  
![ADLS Gen2 存储帐户概述](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>步骤2：选择容器

托管标识应具有在创建工作区时提供的容器（文件系统）的数据访问权限。 可以在 Azure 门户中找到此容器或文件系统。 在 Azure 门户中打开 Azure Synapse 工作区，然后从左侧导航栏中选择 "**概述**" 选项卡。
![ADLS Gen2 存储帐户容器](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


选择相同的容器或文件系统，以向托管标识授予*存储 Blob 数据参与者*角色。
![ADLS Gen2 存储帐户容器选择](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>步骤3：导航到访问控制

选择 "**访问控制（IAM）**"。

![访问控制（IAM）](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>步骤4：添加新的角色分配

选择“+ 添加”****。

![添加新的角色分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>步骤5：选择 RBAC 角色

选择 "**存储 Blob 数据参与者**" 角色。

![选择 RBAC 角色](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>步骤6：选择 Azure AD 安全主体

从 "**分配访问权限**" 下拉菜单中选择**Azure AD 用户、组或服务主体**。

![选择 AAD 安全主体](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>步骤7：搜索托管标识

托管标识的名称也是工作区名称。 在**Select**中输入 Azure Synapse 工作区名称，搜索托管标识。 应会看到列出的托管标识。

![查找托管标识](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>步骤8：选择托管标识

选择**所选成员**的托管标识。 选择 "**保存**" 以添加角色分配。

![选择托管标识](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>步骤9：验证是否已将存储 Blob 数据参与者角色分配给托管标识

选择 "**访问控制（IAM）** "，然后选择 "**角色分配**"。

![验证角色分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

应会在 "**存储 Blob 数据参与者**" 部分下列出托管标识，并向其分配了 "*存储 blob 数据参与者*" 角色。 
![ADLS Gen2 存储帐户容器选择](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>后续步骤

了解有关[工作区托管标识](./synapse-workspace-managed-identity.md)的详细信息
