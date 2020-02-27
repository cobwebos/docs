---
title: 使用混合模式时的已知问题/迁移限制
description: 了解在混合模式下使用 Azure 数据库迁移服务时的已知问题/迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649596"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>使用混合模式时的已知问题/迁移限制

以下各节将介绍与在混合模式下使用 Azure 数据库迁移服务相关的已知问题和限制。

## <a name="installer-fails-to-authenticate"></a>安装程序无法进行身份验证

将证书上传到 AdApp 后，最多需要几分钟的时间才能使用 Azure 进行身份验证。 安装程序将尝试重试一段延迟，但传播延迟可能比重试长，你会看到一条**FailedToGetAccessTokenException**消息。 如果已将证书上传到正确的 AdApp，并在 dmsSettings 中提供了正确的 AppId，请尝试再次运行安装命令。

## <a name="service-offline-after-successful-installation"></a>成功安装后，服务 "脱机"

如果在安装过程成功完成后服务显示为 "脱机"，请尝试执行以下步骤。

1. 在 Azure 门户的 Azure 数据库迁移服务实例中，导航到 "**混合**设置" 选项卡，然后验证是否通过检查已注册辅助角色的网格来注册辅助角色。

    此工作线程的状态应为 "**联机**"，但如果出现问题，它可能会显示为 "**脱机**"。

2. 在辅助计算机上，通过运行以下 PowerShell 命令来检查服务的状态：

    ```
    Get-Service Scenario*
    ```

    此命令提供运行辅助角色的 Windows 服务的状态。 只能有一个结果。 如果工作线程已停止，则可以尝试使用以下 PowerShell 命令重新启动它：

    ```
    Start-Service Scenario*
    ```

    你还可以在 Windows 服务 UI 中检查该服务。

3. 如果 Windows 服务在 "正在运行" 和 "已停止" 之间循环，则辅助角色在启动时遇到问题。 请检查 Azure 数据库迁移服务混合辅助角色日志来确定问题。

    - 安装过程日志存储在运行安装程序可执行文件的文件夹内的 "日志" 文件夹中。
    - Azure 数据库迁移服务混合辅助角色日志存储在安装了辅助角色的文件夹中的**WorkerLogs**文件夹中。 混合辅助角色日志文件的默认位置为**C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**。

## <a name="using-your-own-signed-certificate"></a>使用你自己的签名证书

操作 GenerateCert 生成的证书是自签名证书，根据内部安全策略，这可能是不可接受的。 你可以提供自己的证书，并在 dmsSettings 中提供指纹，而不是使用此证书。 需要将此证书上传到 AdApp，并将其安装在要安装 Azure 数据库迁移服务混合辅助角色的计算机上。 然后，将具有私钥的证书安装到本地计算机证书存储中。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>作为低特权帐户运行辅助角色服务

默认情况下，Azure 数据库迁移服务混合辅助角色服务作为本地系统帐户运行。 只要你使用的帐户具有网络权限，你就可以更改此服务使用的帐户。 若要更改服务 "运行身份" 帐户，请使用以下过程。

1. 通过 Windows 服务或在 PowerShell 中使用停止服务命令停止服务。

2. 更新服务以使用不同的登录帐户。

3. 在 "本地计算机证书的 certmgr.msc" 中，为**Dms 混合应用密钥**和**Dms 方案引擎密钥对**证书的新帐户提供私钥权限。

    a. 打开 certmgr.msc 以查看以下项：

    - DMS 混合应用密钥
    - DMS 混合辅助角色安装密钥
    - DMS 方案引擎密钥对

    b. 右键单击 " **DMS 混合应用密钥**" 条目，指向 "**所有任务**"，然后选择 "**管理私钥**"。

    c. 在 "**安全**" 选项卡上，选择 "**添加**"，然后输入帐户名称。

    d. 使用相同的步骤为新帐户授予对**DMS 方案引擎密钥对**证书的私钥权限。

## <a name="unregistering-the-worker-manually"></a>手动注销辅助角色

如果你不再有权访问该辅助角色计算机，可以通过执行以下步骤，取消注册该工作线程并重复使用 Azure 数据库迁移服务实例：

1. 在 Azure 门户中，转到你的 Azure 数据库迁移服务实例，然后导航到**混合**设置页。

   辅助角色条目将显示在列表中，状态显示为 "**脱机**"。

2. 在辅助角色条目列表的最右侧，选择省略号，然后选择 "**注销**"。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>解决特定迁移方案的问题

以下部分介绍与使用 Azure 数据库迁移服务混合模式执行联机迁移相关的特定于方案的问题。

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>联机迁移到 Azure SQL 数据库托管实例

**CPU 使用率高**

**问题**：对于到 SQL 数据库托管实例的联机迁移，运行混合辅助角色的计算机在有太多备份或备份太大的情况下，运行混合辅助角色的计算机将遇到较高的 CPU 使用率。

**缓解**：若要缓解此问题，请使用压缩的备份，拆分迁移以便使用多个共享，或纵向扩展运行混合辅助角色的计算机。
