---
title: 保护您的 Synapse 工作区（预览版）
description: 本文将教您如何使用角色和访问控制来控制 Synapse 工作区中的活动和数据访问。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 94699f2153fa8d1df3ab85c184f32792c7ae0b59
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428870"
---
# <a name="secure-your-synapse-workspace-preview"></a>保护您的 Synapse 工作区（预览版）

本文将教您如何使用角色和访问控制来控制活动和访问数据。 通过按照这些说明，Azure 突触分析中的访问控制得到了简化。 您只需将用户添加到三个安全组之一。

## <a name="overview"></a>概述

要保护 Synapse 工作区（预览），您将遵循配置以下项的模式：

- Azure 角色（如内置角色，如所有者、参与者等）
- 突触角色 – 这些角色是 Synapse 独有的，不基于 Azure 角色。 有以下三个角色：
  - 突触工作区管理员
  - 突触 SQL 管理员
  - 突触火花管理员
- Azure 数据存储第 2 代 （ADLSGEN2） 中的数据的访问控制。
- Synapse SQL 和 Spark 数据库的访问控制

## <a name="steps-to-secure-a-synapse-workspace"></a>保护 Synapse 工作区的步骤

本文档使用标准名称来简化说明。 用您选择的任何名称替换它们。

|设置 | 示例值 | 说明 |
| :------ | :-------------- | :---------- |
| **突触工作区** | WS1 |  Synapse 工作区的名称。 |
| **ADLSGEN2 账户** | STG1 | 要与您的工作区一起使用的 ADLS 帐户。 |
| **容器** | CNT1 | 默认情况下工作区将使用的 STG1 中的容器。 |
| **活动目录租户** | contoso | 活动目录租户名称。|
||||

## <a name="step-1-set-up-security-groups"></a>第 1 步：设置安全组

为工作区创建和填充三个安全组：

- **WS1\_WSAdmins** – 适用于需要完全控制工作区的用户
- **WS1\_SparkAdmins** – 适用于需要完全控制工作区 Spark 方面的用户
- **WS1\_SQLAdmins** – 适用于需要完全控制工作区 SQL 方面的用户
- 将**\_WS1 管理员**添加到**WS1\_SQLAdmin**
- 将**\_WS1 管理员**添加到**WS1\_SparkAdmin**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>第 2 步：准备您的数据存储第 2 代帐户

标识有关存储的信息：

- 用于工作区的 ADLSGEN2 帐户。 本文档称其为 STG1。  STG1 被视为工作区的"主"存储帐户。
- 默认情况下，Synapse 工作区将使用的 WS1 中的容器。 本文档称其为 CNT1。  此容器用于：
  - 存储 Spark 表的备份数据文件
  - Spark 作业的执行日志

- 使用 Azure 门户，在 CNT1 上分配以下安全组

  - 将**WS1\_WSAdmin 分配给**存储 Blob**数据参与者**角色
  - 将**WS1\_SparkAdmin 分配给**存储 Blob**数据参与者**角色
  - 将**WS1\_SQLAdmin 分配给**存储 Blob**数据参与者**角色

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>第 3 步：创建和配置 Synape 工作区

在 Azure 门户中，创建 Synape 工作区：

- 命名工作区 WS1
- 为存储帐户选择 STG1
- 为用作"文件系统"的容器选择 CNT1。
- 在 Synapse 工作室打开 WS1
- 选择"**管理** > **访问控制**"将安全组分配给以下 Synapse 角色。
  - 将**WS1\_WSAdmin 分配给**Synapse 工作区管理员
  - 将**WS1\_SparkAdmin 分配给**Synapse Spark 管理员
  - 将**WS1\_SQLAdmin 分配给**Synapse SQL 管理员

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>第 4 步：配置数据湖存储 Gen2 供 Synapse 工作区使用

Synapse 工作区需要访问 STG1 和 CNT1，以便它可以运行管道并执行系统任务。

- 打开 Azure 门户
- 找到 STG1
- 导航到 CNT1
- 确保 WS1 的 MSI 已分配给 CNT1 上的**Azure Blob 数据参与者**角色
  - 如果未看到它分配，请分配它。
  - MSI 的名称与工作区相同。 在这种情况下，它将是&quot;WS1&quot;。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>第 5 步：为 SQL 池配置管理员访问权限

- 打开 Azure 门户
- 导航到 WS1
- 在 **"设置"** 下，单击**SQL 活动目录管理员**
- 单击 **"设置管理员"** 并选择\_WS1 SQLAdmin

## <a name="step-6-maintaining-access-control"></a>第 6 步：保持访问控制

配置已完成。

现在，为了管理用户的访问权限，您可以将用户添加到三个安全组。

尽管您可以手动将用户分配给 Synapse 角色，但如果这样做，它将无法一致地配置内容。 相反，仅向安全组添加或删除用户。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>第 7 步：验证角色中用户的访问

每个角色中的用户需要完成以下步骤：

|   | 步骤 | 工作区管理员 | 火花管理员 | SQL 管理员 |
| --- | --- | --- | --- | --- |
| 1 | 将镶木地板文件上载到 CNT1 | YES | YES | YES |
| 2 | 按需使用 SQL 读取镶木地板文件 | YES | 是 | YES |
| 3 | 创建火花池 | 是 [1] | 是 [1] | 是  |
| 4 | 使用笔记本读取镶木地板文件 | YES | YES | 是 |
| 5 | 从笔记本创建管道，并触发管道立即运行 | YES | 是 | 是 |
| 6 | 创建 SQL 池并运行 SQL 脚本&quot;，如 SELECT 1&quot; | 是 [1] | 是 | 是[1] |

> [!NOTE]
> [1] 要创建 SQL 或 Spark 池，用户必须在 Synapse 工作区上至少具有"参与者"角色。
> [!TIP]
>
> - 根据角色的不同，某些步骤将故意不允许。
> - 请记住，如果安全性未完全配置，则某些任务可能会失败。 这些任务在表中注明。

## <a name="step-8-network-security"></a>第 8 步：网络安全

配置工作区防火墙、虚拟网络和[专用链路](../../sql-database/sql-database-private-endpoint-overview.md)。

## <a name="step-9-completion"></a>第 9 步：完成

您的工作区现已完全配置和保护。

## <a name="how-roles-interact-with-synapse-studio"></a>角色如何与 Synapse 工作室交互

Synapse 工作室将根据用户角色的不同表现。 如果用户未分配到提供适当访问权限的角色，则某些项目可能被隐藏或禁用。 下表总结了对 Synapse 工作室的影响。

| 任务 | 工作区管理员 | 火花管理员 | SQL 管理员 |
| --- | --- | --- | --- |
| 打开同步工作室 | YES | YES | YES |
| 查看主页中心 | YES | YES | YES |
| 查看数据中心 | YES | YES | YES |
| 数据中心 /请参阅链接的 ADLSGen2 帐户和容器 | 是 [1] | 是[1] | 是[1] |
| 数据中心/请参阅数据库 | YES | YES | YES |
| 数据中心/查看数据库中的对象 | YES | YES | YES |
| 数据中心/访问 SQL 池数据库中的数据 | YES   | 是   | YES   |
| 数据中心/访问 SQL 按需数据库中的数据 | 是 [2]  | 是  | 是 [2]  |
| 数据中心/Spark 数据库中的访问数据 | 是 [2] | 是 [2] | 是 [2] |
| 使用"开发"中心 | YES | YES | YES |
| 开发中心/作者 SQL 脚本 | YES | 是 | YES |
| 开发中心/作者 Spark 作业定义 | YES | YES | 是 |
| 开发中心/作者笔记本 | YES | YES | 是 |
| 开发中心/作者数据流 | YES | 是 | 是 |
| 使用协调中心 | YES | YES | YES |
| 协调集线器/使用管道 | YES | 是 | 是 |
| 使用管理中心 | YES | YES | YES |
| 管理中心/SQL 池 | YES | 是 | YES |
| 管理中心/火花池 | YES | YES | 是 |
| 管理中心/触发器 | YES | 是 | 是 |
| 管理中心/链接服务 | YES | YES | YES |
| 管理中心/访问控制（将用户分配给 Synapse 工作区角色） | YES | 是 | 是 |
| 管理中心/集成运行时 | YES | YES | YES |

> [!NOTE]
> [1] 容器中对数据的访问取决于 ADLSGen2 [2] SQL OD 表中的访问控制，Spark 表将其数据存储在 ADLSGen2 中，并且访问需要对 ADLSGen2 具有适当的权限。

## <a name="next-steps"></a>后续步骤

创建[突触工作区](../quickstart-create-workspace.md)
