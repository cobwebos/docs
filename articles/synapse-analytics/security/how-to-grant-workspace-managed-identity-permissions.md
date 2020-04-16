---
title: 如何授予 Azure Synapse 工作区中的托管标识的权限
description: 一篇介绍如何在 Azure Synapse 工作区中配置托管标识权限的文章。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428012"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>授予工作区托管标识的权限（预览）

本文介绍如何在 Azure 突触工作区中授予托管标识的权限。 权限又允许通过 Azure 门户访问工作区中的 SQL 池和 ADLS gen2 存储帐户。

>[!NOTE]
>此工作区托管标识将通过本文档的其余部分称为托管标识。

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>向 SQL 池授予托管标识权限

托管标识向工作区中的 SQL 池授予权限。 授予权限后，可以协调执行 SQL 池相关活动的管道。 使用 Azure 门户创建 Azure 突触工作区时，可以在 SQL 池上授予托管标识 CONTROL 权限。

在创建 Azure 同步工作区时选择 **"安全 + 网络**"。 然后选择**在 SQL 池上对工作区的托管标识授予控制**。

![SQL 池上的 CONTROL 权限](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>将托管标识权限授予 ADLS 第 2 代存储帐户

创建 Azure 突触工作区需要 ADLS gen2 存储帐户。 要在 Azure Synapse 工作区中成功启动 Spark 池，Azure Synapse 托管标识需要此存储帐户上的*存储 Blob 数据参与者*角色。 Azure Synapse 中的管道业务流程也受益于此角色。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>在工作区创建期间授予托管标识的权限

使用 Azure 突触工作区创建 Azure Synapse 工作区后，Azure Synapse 将尝试将存储 Blob 数据参与者角色授予托管标识。 您可以在 **"基础知识"** 选项卡中提供 ADLS 第 2 代存储帐户详细信息。

![工作区创建流中的基础知识选项卡](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

在**帐户名称**和**文件系统名称**中选择 ADLS gen2 存储帐户和文件系统。

![提供 ADLS 第 2 代存储帐户详细信息](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

如果工作区创建者也是 ADLS gen2 存储帐户**的所有者**，则 Azure Synapse 将存储*Blob 数据参与者*角色分配给托管标识。 您将在下面看到您输入的存储帐户详细信息。

![成功分配存储 Blob 数据参与者](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

如果工作区创建者不是 ADLS gen2 存储帐户的所有者，则 Azure Synapse 不会将存储*Blob 数据参与者*角色分配给托管标识。 存储帐户详细信息下方显示的消息通知工作区创建者他们没有足够的权限将*存储 Blob 数据参与者*角色授予托管标识。

![失败的存储 Blob 数据参与者分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

如消息指出，除非*将存储 Blob 数据参与者*分配给托管标识，否则无法创建 Spark 池。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>在工作区创建后授予托管标识的权限

在工作区创建期间，如果不将存储 Blob*数据参与者*分配给托管标识，则 ADLS gen2 存储帐户**的所有者**将该角色手动分配给该标识。 以下步骤将帮助您完成手动作业。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>第 1 步：导航到 Azure 门户中的 ADLS 第 2 代存储帐户

在 Azure 门户中，打开 ADLS gen2 存储帐户，然后从左侧导航中选择 **"概述**"。 您只需在容器或文件系统级别分配*存储 Blob 数据参与者*角色。 选择“容器”****。  
![ADLS 第 2 代存储帐户概述](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>第 2 步：选择容器

托管标识应具有对创建工作区时提供的容器（文件系统）的数据访问权限。 您可以在 Azure 门户中找到此容器或文件系统。 在 Azure 门户中打开 Azure 同步工作区，然后从左侧导航中选择 **"概述"** 选项卡。
![ADLS 第 2 代存储帐户容器](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


选择同一容器或文件系统，将存储*Blob 数据参与者*角色授予托管标识。
![ADLS 第 2 代存储帐户容器选择](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>第 3 步：导航到访问控制

选择**访问控制 （IAM）。**

![访问控制（IAM）](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>步骤 4：添加新的角色分配

选择“+ 添加”****。

![添加新角色分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>第 5 步：选择 RBAC 角色

选择**存储 Blob 数据参与者**角色。

![选择 RBAC 角色](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>第 6 步：选择 Azure AD 安全主体

从 **"分配"下拉列表的访问权限**中选择**Azure AD 用户、组或服务主体**。

![选择 AAD 安全主体](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>第 7 步：搜索托管标识

托管标识的名称也是工作区名称。 通过在 **"选择**"中输入 Azure Synapse 工作区名称来搜索托管标识。 您应该会看到列出的托管标识。

![查找托管标识](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>第 8 步：选择托管标识

选择"**选定成员**"的托管标识。 选择 **"保存"** 以添加角色分配。

![选择托管标识](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>第 9 步：验证存储 Blob 数据参与者角色是否分配给托管标识

选择**访问控制 （IAM），** 然后选择**角色分配**。

![验证角色分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

您应该会看到托管标识列在 **"存储 Blob 数据参与者"** 部分下，并为其分配了 *"存储 Blob 数据参与者"* 角色。 
![ADLS 第 2 代存储帐户容器选择](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>后续步骤

了解有关[工作区托管标识的更多信息](./synapse-workspace-managed-identity.md)
