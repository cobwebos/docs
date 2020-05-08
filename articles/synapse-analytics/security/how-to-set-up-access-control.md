---
title: 保护你的 Synapse 工作区（预览）
description: 本文介绍如何使用角色和访问控制来控制活动和访问 Synapse 工作区中的数据。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 383a28e90c093c0885c34af03371608261ece110
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692631"
---
# <a name="secure-your-synapse-workspace-preview"></a>保护你的 Synapse 工作区（预览）

本文介绍如何使用角色和访问控制来控制活动和访问数据。 按照这些说明操作，Azure Synapse Analytics 中的访问控制得以简化。 只需向三个安全组中的一个添加和删除用户。

## <a name="overview"></a>概述

若要保护 Synapse 工作区（预览版），请遵循配置以下项的模式：

- Azure 角色（如 "所有者"、"参与者" 等内置项）
- Synapse 角色–这些角色对于 Synapse 是唯一的，不基于 Azure 角色。 其中有三个角色：
  - Synapse 工作区管理员
  - Synapse SQL 管理
  - Synapse Spark 管理
- Azure Data Lake Storage 第2代中的数据的访问控制2（ADLSGEN2）。
- Synapse SQL 和 Spark 数据库的访问控制

## <a name="steps-to-secure-a-synapse-workspace"></a>保护 Synapse 工作区的步骤

本文档使用标准名称来简化说明。 将它们替换为所选的任何名称。

|设置 | 示例值 | 说明 |
| :------ | :-------------- | :---------- |
| **Synapse 工作区** | WS1 |  Synapse 工作区将具有的名称。 |
| **ADLSGEN2 帐户** | STG1 | 要与工作区一起使用的 ADLS 帐户。 |
| **容器** | CNT1 | 默认情况下，工作区将使用的 STG1 中的容器。 |
| **Active directory 租户** | contoso | active directory 租户名称。|
||||

## <a name="step-1-set-up-security-groups"></a>步骤1：设置安全组

为工作区创建和填充三个安全组：

- **WS1\_WSAdmins** –适用于需要完全控制工作区的用户
- **WS1\_SparkAdmins** –适用于需要完全控制工作区 Spark 方面的用户
- **WS1\_SQLAdmins** –适用于需要完全控制工作区的 SQL 各个方面的用户
- 将**WS1\_WSAdmins**添加**到\_WS1 SQLAdmins**
- 将**WS1\_WSAdmins**添加**到\_WS1 SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>步骤2：准备 Data Lake Storage Gen2 帐户

确定有关存储的信息：

- 要用于工作区的 ADLSGEN2 帐户。 本文档对其进行 STG1 调用。  STG1 被视为工作区的 "主" 存储帐户。
- 默认情况下，Synapse 工作区将使用 WS1 中的容器。 本文档对其进行 CNT1 调用。  此容器用于：
  - 为 Spark 表存储支持的数据文件
  - Spark 作业的执行日志

- 使用 Azure 门户在 CNT1 上为安全组分配以下角色

  - 将**WS1\_WSAdmins**分配到**存储 Blob 数据参与者**角色
  - 将**WS1\_SparkAdmins**分配到**存储 Blob 数据参与者**角色
  - 将**WS1\_SQLAdmins**分配到**存储 Blob 数据参与者**角色

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>步骤3：创建和配置 Synapse 工作区

在 Azure 门户中，创建 Synapse 工作区：

- 命名工作区 WS1
- 为存储帐户选择 STG1
- 为用作 "filesystem" 的容器选择 CNT1。
- 在 Synapse Studio 中打开 WS1
- 选择 "**管理** > **访问控制**" 将安全组分配给以下 Synapse 角色。
  - 将**WS1\_WSAdmins**分配到 Synapse 工作区管理员
  - 将**WS1\_SparkAdmins**分配到 Synapse Spark 管理员
  - 将**WS1\_SQLAdmins**分配到 Synapse SQL 管理员

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>步骤4：配置 Data Lake Storage Gen2 以供 Synapse 工作区使用

Synapse 工作区需要对 STG1 和 CNT1 的访问权限，以便它可以运行管道和执行系统任务。

- 打开 Azure 门户
- 找到 STG1
- 导航到 CNT1
- 确保将 WS1 的 MSI （托管服务标识）分配给 CNT1 上的**Azure Blob 数据参与者**角色。
  - 如果看不到它，请对其进行分配。
  - MSI 的名称与工作区相同。 在这种情况下，它&quot;是&quot;WS1。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>步骤5：配置 SQL 池的管理员访问权限

- 打开 Azure 门户
- 导航到 WS1
- 在 "**设置**" 下，单击 " **SQL Active Directory 管理**"
- 单击 "**设置管理员**"，\_然后选择 WS1 SQLAdmins

## <a name="step-6-maintaining-access-control"></a>步骤6：维护访问控制

配置已完成。

现在，要管理用户的访问权限，你可以将用户添加到三个安全组并将其删除。

尽管可以手动将用户分配到 Synapse 角色，但如果这样做，则不会对其进行一致的配置。 而只是在安全组中添加或删除用户。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>步骤7：验证角色中用户的访问权限

每个角色中的用户需要完成以下步骤：

|   | 步骤 | 工作区管理员 | Spark 管理员 | SQL 管理员 |
| --- | --- | --- | --- | --- |
| 1 | 将 parquet 文件上传到 CNT1 | YES | YES | YES |
| 2 | 使用 SQL 点播读取 parquet 文件 | YES | 是 | YES |
| 3 | 创建 Spark 池 | 是 [1] | 是 [1] | 是  |
| 4 | 使用笔记本读取 parquet 文件 | YES | YES | 是 |
| 5 | 从笔记本创建管道，并触发管道立即运行 | YES | 是 | 是 |
| 6 | 创建 SQL 池并运行 SQL 脚本，如&quot;SELECT 1&quot; | 是 [1] | 是 | 是 [1] |

> [!NOTE]
> [1] 若要创建 SQL 或 Spark 池，用户必须在 Synapse 工作区上至少具有 "参与者" 角色。
> [!TIP]
>
> - 根据角色，将有意不允许某些步骤。
> - 请记住，如果未完全配置安全性，某些任务可能会失败。 表中注明了这些任务。

## <a name="step-8-network-security"></a>步骤8：网络安全

配置工作区防火墙、虚拟网络和[专用链接](../../sql-database/sql-database-private-endpoint-overview.md)。

## <a name="step-9-completion"></a>步骤9：完成

你的工作区现已完全配置并受到保护。

## <a name="how-roles-interact-with-synapse-studio"></a>角色如何与 Synapse Studio 交互

Synapse Studio 将根据用户角色的行为有所不同。 如果用户未分配给授予适当访问权限的角色，某些项可能会隐藏或禁用。 下表总结了对 Synapse Studio 的影响。

| 任务 | 工作区管理员 | Spark 管理员 | SQL 管理员 |
| --- | --- | --- | --- |
| 打开 Synapse Studio | YES | YES | YES |
| 查看本中心 | YES | YES | YES |
| 查看数据中心 | YES | YES | YES |
| 数据中心/请参阅链接的 ADLSGen2 帐户和容器 | 是 [1] | 是 [1] | 是 [1] |
| 数据中心/请参阅数据库 | YES | YES | YES |
| 数据中心/查看数据库中的对象 | YES | YES | YES |
| 数据中心/访问 SQL 池中数据库中的数据 | YES   | 是   | YES   |
| 数据中心/访问 SQL 点播数据库中的数据 | 是 [2]  | 是  | 是 [2]  |
| Spark 数据库中的数据中心/访问数据 | 是 [2] | 是 [2] | 是 [2] |
| 使用开发中心 | YES | YES | YES |
| 开发中心/作者 SQL 脚本 | YES | 是 | YES |
| 开发中心/作者 Spark 作业定义 | YES | YES | 是 |
| 开发中心/作者笔记本 | YES | YES | 是 |
| 开发中心/作者数据流 | YES | 是 | 是 |
| 使用协调中心 | YES | YES | YES |
| 安排中心/使用管道 | YES | 是 | 是 |
| 使用管理中心 | YES | YES | YES |
| 管理中心/SQL 池 | YES | 是 | YES |
| 管理中心/Spark 池 | YES | YES | 是 |
| 管理中心/触发器 | YES | 是 | 是 |
| 管理中心/链接服务 | YES | YES | YES |
| 管理中心/访问控制（将用户分配到 Synapse 工作区角色） | YES | 是 | 是 |
| 管理中心/集成运行时 | YES | YES | YES |
| 使用监视集线器 | YES | YES | YES |
| 监视集线器/业务流程/管道运行  | YES | 是 | 是 |
| 监视集线器/业务流程/触发器运行  | YES | 是 | 是 |
| 监视集线器/业务流程/集成运行时  | YES | YES | YES |
| 监视集线器/活动/Spark 应用程序 | YES | YES | 是  |
| 监视集线器/活动/SQL 请求 | YES | 是 | YES |
| 监视集线器/活动/Spark 池 | YES | YES | 是  |
| 监视集线器/触发器 | YES | 是 | 是 |
| 管理中心/链接服务 | YES | YES | YES |
| 管理中心/访问控制（将用户分配到 Synapse 工作区角色） | YES | 是 | 是 |
| 管理中心/集成运行时 | YES | YES | YES |


> [!NOTE]
> [1] 对容器中的数据的访问权限取决于 ADLSGen2 中的访问控制。 </br>
> [2] SQL OD 表和 Spark 表将其数据存储在 ADLSGen2 中，access 需要 ADLSGen2 的适当权限。

## <a name="next-steps"></a>后续步骤

创建[Synapse 工作区](../quickstart-create-workspace.md)
