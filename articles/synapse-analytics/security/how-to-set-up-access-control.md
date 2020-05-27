---
title: 保护 Synapse 工作区（预览）
description: 本文将介绍如何使用角色和访问控制来控制 Synapse 工作区中的活动和数据访问。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: d02cd12552b3664dd7acaae0142fc939ee57f5f6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591975"
---
# <a name="secure-your-synapse-workspace-preview"></a>保护 Synapse 工作区（预览）

本文将介绍如何使用角色和访问控制来控制活动和数据访问。 通过遵循这些说明，可以简化 Azure Synapse Analytics 中的访问控制。 只需要在三个安全组之一中添加和删除用户。

## <a name="overview"></a>概述

为了保护 Synapse 工作区（预览），将遵循一种模式来配置以下项：

- Azure 角色（如“所有者”、“参与者”等内置角色）
- Synapse 角色 - 这些角色对于 Synapse 来说是唯一的，并且不是基于 Azure 角色的。 有以下三个角色：
  - Synapse 工作区管理员
  - Synapse SQL 管理员
  - Apache Spark for Azure Synapse Analytics 管理员
- 对 Azure Data Lake Storage Gen 2 (ADLSGEN2) 中数据的访问控制。
- 对 Synapse SQL 和 Spark 数据库的访问控制

## <a name="steps-to-secure-a-synapse-workspace"></a>保护 Synapse 工作区的步骤

本文档使用标准名称来简化说明。 请将它们替换为你选择的任何名称。

|设置 | 示例值 | 说明 |
| :------ | :-------------- | :---------- |
| **Synapse 工作区** | WS1 |  Synapse 工作区的名称。 |
| **ADLSGEN2 帐户** | STG1 | 用于工作区的 ADLS 帐户。 |
| **容器** | CNT1 | STG1 中的容器，由工作区默认使用。 |
| **Active Directory 租户** | contoso | Active Directory 租户名称。|
||||

## <a name="step-1-set-up-security-groups"></a>步骤 1：创建安全组

为工作区创建并填充三个安全组：

- WS1\_WSAdmins - 对于需要完全控制工作区的用户
- WS1\_SparkAdmins - 对于需要完全控制工作区的 Spark 方面的用户
- WS1\_SQLAdmins - 对于需要完全控制工作区的 SQL 方面的用户
- 将 WS1\_WSAdmins 添加到 WS1\_SQLAdmins
- 将 WS1\_WSAdmins 添加到 WS1\_SparkAdmins

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>步骤 2：准备 Data Lake Storage Gen2 帐户

识别关于存储的以下信息：

- 用于工作区的 ADLSGEN2 帐户。 本文档称之为“STG1”。  STG1 被视为工作区的“主”存储帐户。
- WS1 中的容器，由 Synapse 工作区默认使用的。 本文档称之为“CNT1”。  此容器用于：
  - 存储 Spark 表的备份数据文件
  - Spark 作业的执行日志

- 使用 Azure 门户，为安全组分配 CNT1 上的以下角色

  - 将 WS1\_WSAdmins 分配到“存储 Blob 数据参与者”角色
  - 将 WS1\_SparkAdmins 分配到“存储 Blob 数据参与者”角色
  - 将 WS1\_SQLAdmins 分配到“存储 Blob 数据参与者”角色

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>第 3 步：创建和配置 Synapse 工作区

在 Azure 门户中，创建 Synapse 工作区：

- 将工作区命名为“WS1”
- 选择“STG1”作为“存储帐户”
- 选择“CNT1”作为要用作“文件系统”的容器。
- 在 Synapse Studio 中打开 WS1
- 依次选择“管理” > “访问控制”，将安全组分配到以下 Synapse 角色。
  - 将 WS1\_WSAdmins 分配到 Synapse 工作区管理员
  - 将 WS1\_SparkAdmins 分配到 Synapse Spark 管理员
  - 将 WS1\_SQLAdmins 分配到 Synapse SQL 管理员

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>第 4 步：配置 Data Lake Storage Gen2 以供 Synapse 工作区使用

Synapse 工作区需要访问 STG1 和 CNT1，以便能够运行管道并执行系统任务。

- 打开 Azure 门户
- 找到 STG1
- 转到 CNT1
- 确保将 WS1 的 MSI（托管服务标识）分配到 CNT1 上的“存储 Blob 数据参与者”角色
  - 如果没有看到它被分配，请分配它。
  - MSI 与工作区同名。 在这种情况下，它的名称是“WS1”&quot;&quot;。

## <a name="step-5-configure-admin-access-for-sql-pools"></a>第 5 步：为 SQL 池配置管理员访问权限

- 打开 Azure 门户
- 转到 WS1
- 在“设置”下，单击“SQL Active Directory 管理员”
- 单击“设置管理员”，然后选择“WS1\_SQLAdmins”

## <a name="step-6-maintaining-access-control"></a>第 6 步：维护访问控制

配置已完成。

现在，若要管理用户的访问权限，可以在三个安全组中添加和删除用户。

尽管可以手动将用户分配到 Synapse 角色，但是如果你这样做，将会导致配置不一致。 相反，请只在安全组中添加或删除用户。

## <a name="step-7-verify-access-for-users-in-the-roles"></a>第 7 步：验证角色中的用户的访问权限

每个角色的用户都需要完成以下步骤：

|   | 步骤 | 工作区管理员 | Spark 管理员 | SQL 管理员 |
| --- | --- | --- | --- | --- |
| 1 | 将 parquet 文件上传到 CNT1 中 | YES | YES | YES |
| 2 | 使用 SQL On-Demand 读取 parquet 文件 | YES | 是 | YES |
| 3 | 创建 Spark 池 | 是 [1] | 是 [1] | 是  |
| 4 | 使用笔记本读取 parquet 文件 | YES | YES | 是 |
| 5 | 通过笔记本创建管道，并触发管道立即运行 | YES | 是 | 是 |
| 6 | 创建 SQL 池，并运行 &quot;SELECT 1&quot; 等 SQL 脚本 | 是 [1] | 是 | 是 [1] |

> [!NOTE]
> [1] 若要创建 SQL 或 Spark 池，用户至少必须在 Synapse 工作区上有“参与者”角色。
> [!TIP]
>
> - 有些步骤是故意不允许执行的，具体视角色而定。
> - 请注意，如果没有完全配置安全性，有些任务可能会失败。 表中注明了这些任务。

## <a name="step-8-network-security"></a>第 8 步：网络安全

配置工作区防火墙、虚拟网络和[专用链接](../../sql-database/sql-database-private-endpoint-overview.md)。

## <a name="step-9-completion"></a>第 9 步：Completion

你的工作区现在已完全配置并受到保护。

## <a name="how-roles-interact-with-synapse-studio"></a>角色如何与 Synapse Studio 交互

Synapse Studio 的行为因用户角色而异。 如果用户没有分配到授予相应访问权限的角色，则某些项可能会被隐藏或禁用。 下表总结了对 Synapse Studio 的影响。

| 任务 | 工作区管理员 | Spark 管理员 | SQL 管理员 |
| --- | --- | --- | --- |
| 打开 Synapse Studio | YES | YES | YES |
| 查看主页中心 | YES | YES | YES |
| 查看数据中心 | YES | YES | YES |
| 数据中心/查看关联的 ADLS Gen2 帐户和容器 | 是 [1] | 是 [1] | 是 [1] |
| 数据中心/查看数据库 | YES | YES | YES |
| 数据中心/查看数据库中的对象 | YES | YES | YES |
| 数据中心/访问 SQL 池数据库中的数据 | YES   | 是   | YES   |
| 数据中心/访问 SQL On-Demand 数据库中的数据 | 是 [2]  | 是  | 是 [2]  |
| 数据中心/访问 Spark 数据库中的数据 | 是 [2] | 是 [2] | 是 [2] |
| 使用开发中心 | YES | YES | YES |
| 开发中心/创作 SQL 脚本 | YES | 是 | YES |
| 开发中心/创作 Spark 作业定义 | YES | YES | 是 |
| 开发中心/创作笔记本 | YES | YES | 是 |
| 开发中心/创作数据流 | YES | 是 | 是 |
| 使用编排中心 | YES | YES | YES |
| 编排中心/使用管道 | YES | 是 | 是 |
| 使用管理中心 | YES | YES | YES |
| 管理中心/SQL 池 | YES | 是 | YES |
| 管理中心/Spark 池 | YES | YES | 是 |
| 管理中心/触发器 | YES | 是 | 是 |
| 管理中心/链接服务 | YES | YES | YES |
| 管理中心/访问控制（将用户分配到 Synapse 工作区角色） | YES | 是 | 是 |
| 管理中心/集成运行时 | YES | YES | YES |
| 使用监视中心 | YES | YES | YES |
| 监视中心/编排/管道运行  | YES | 是 | 是 |
| 监视中心/编排/触发器运行  | YES | 是 | 是 |
| 监视中心/编排/集成运行时  | YES | YES | YES |
| 监视中心/活动/Spark 应用 | YES | YES | 是  |
| 监视中心/活动/SQL 请求 | YES | 是 | YES |
| 监视中心/活动/Spark 池 | YES | YES | 是  |
| 监视中心/触发器 | YES | 是 | 是 |
| 管理中心/链接服务 | YES | YES | YES |
| 管理中心/访问控制（将用户分配到 Synapse 工作区角色） | YES | 是 | 是 |
| 管理中心/集成运行时 | YES | YES | YES |


> [!NOTE]
> [1] 对容器中数据的访问权限取决于 ADLS Gen2 中的访问控制。 </br>
> [2] SQL OD 表和 Spark 表将数据存储在 ADLS Gen2 中，需要具有对 ADLS Gen2 的相应权限才能进行访问。

## <a name="next-steps"></a>后续步骤

创建 [Synapse 工作区](../quickstart-create-workspace.md)
