---
title: 使用混合模式的已知问题/迁移限制
description: 在混合模式下使用 Azure 数据库迁移服务，了解已知问题/迁移限制。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649596"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>使用混合模式的已知问题/迁移限制

以下各节介绍了在混合模式下使用 Azure 数据库迁移服务相关的已知问题和限制。

## <a name="installer-fails-to-authenticate"></a>安装程序无法进行身份验证

将证书上载到 AdApp 后，最多延迟几分钟才能使用 Azure 进行身份验证。 安装程序将尝试重试一些延迟，但传播延迟可能比重试长，您将看到**一条"失败到GetAccessTokenException"** 消息。 如果证书已上载到正确的 AdApp，并且 dmsSettings.json 中提供了正确的 AppId，请尝试再次运行安装命令。

## <a name="service-offline-after-successful-installation"></a>成功安装后"脱机"服务

如果服务在安装过程成功完成后显示为脱机，请尝试使用以下步骤。

1. 在 Azure 门户中，在 Azure 数据库迁移服务实例中，导航到**混合**设置选项卡，然后通过检查已注册工作人员的网格来验证工作人员是否注册。

    此工作人员的状态应**为联机**，但如果出现问题，可能会显示为**脱机**。

2. 在辅助计算机上，通过运行以下 PowerShell 命令来检查服务的状态：

    ```
    Get-Service Scenario*
    ```

    此命令提供运行辅助角色的 Windows 服务的状态。 应该只有一个结果。 如果工作人员已停止，则可以使用以下 PowerShell 命令尝试重新启动它：

    ```
    Start-Service Scenario*
    ```

    您还可以在 Windows 服务 UI 中检查该服务。

3. 如果 Windows 服务在"正在运行"和"已停止"之间循环，则工作人员在启动时遇到问题。 检查 Azure 数据库迁移服务混合工作日志以确定问题。

    - 安装过程日志存储在运行安装程序可执行文件的文件夹中的"日志"文件夹中。
    - Azure 数据库迁移服务混合辅助角色日志存储在**辅助日志**文件夹中，该文件夹中安装辅助角色。 混合辅助角色日志文件的默认位置是**C：\程序文件\数据库迁移服务混合_WorkerLogs**。

## <a name="using-your-own-signed-certificate"></a>使用您自己的签名证书

操作 GenerateCert 生成的证书是自签名证书，根据内部安全策略可能无法接受。 您可以提供自己的证书并在 dmsSettings.json 中提供指纹，而不是使用此证书。 此证书需要上载到 AdApp 并安装在要安装 Azure 数据库迁移服务混合工作线程的计算机上。 然后，使用私钥将此证书安装到本地计算机证书存储中。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>将辅助服务作为低特权帐户运行

默认情况下，Azure 数据库迁移服务混合辅助工作线程服务作为本地系统帐户运行。 只要您使用的帐户具有网络权限，就可以更改用于此服务的帐户。 要更改服务"以"身份运行帐户，请使用以下过程。

1. 通过 Windows 服务或使用 PowerShell 中的"停止服务"命令停止服务。

2. 更新服务以使用不同的登录帐户。

3. 在本地计算机证书证书中，为**DMS 混合应用密钥**和**DMS 方案引擎密钥对**证书的新帐户授予私钥权限。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 打开证书，查看以下键：

    - DMS 混合应用密钥
    - DMS 混合辅助角色设置密钥
    - DMS 方案引擎密钥对

    b.保留“数据库类型”设置，即设置为“共享”。 右键单击**DMS 混合应用密钥**条目，指向**所有任务**，然后选择"**管理私钥**"。

    c. 在 **"安全"** 选项卡上，选择 **"添加**"，然后输入帐户的名称。

    d.单击“下一步”。 使用相同的步骤将新帐户的私钥权限授予**DMS 方案引擎密钥对**证书。

## <a name="unregistering-the-worker-manually"></a>手动取消注册工作人员

如果不再有权访问辅助计算机，则可以通过执行以下步骤取消注册辅助角色并重用 Azure 数据库迁移服务实例：

1. 在 Azure 门户中，到达 Azure 数据库迁移服务实例，然后导航到**混合**设置页。

   您的工作人员条目将显示在列表中，状态显示为 **"脱机**"。

2. 在辅助角色条目列表的最右侧，选择椭圆，然后选择 **"取消注册**"。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>解决特定迁移方案的问题

以下各节介绍与使用 Azure 数据库迁移服务混合模式执行联机迁移相关的特定于方案的问题。

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>联机迁移到 Azure SQL 数据库托管实例

**CPU 使用率较高**

**问题**：对于联机迁移到 SQL 数据库托管实例，如果备份太多或备份太大，运行混合工作台的计算机将遇到高 CPU 使用率。

**缓解**：为了缓解此问题，请使用压缩备份、拆分迁移以便使用多个共享，或向上扩展运行混合辅助角色的计算机。
