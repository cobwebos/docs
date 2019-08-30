---
title: 自动缩放 Windows 虚拟桌面预览会话主机-Azure
description: 描述如何为 Windows 虚拟桌面预览会话主机设置自动缩放脚本。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 7babfca617ab42da615518726d1b1d4cafe112b5
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163241"
---
# <a name="automatically-scale-session-hosts"></a>自动缩放会话主机

对于 Azure 中的许多 Windows 虚拟桌面预览版部署, 虚拟机成本表示 Windows 虚拟桌面总部署成本的重要部分。 为了降低成本, 最好关闭并解除分配会话主机虚拟机 (Vm) 在非高峰使用时间, 并在高峰使用时间内重新启动它们。

本文使用简单的缩放脚本来自动缩放 Windows 虚拟桌面环境中的会话主机虚拟机。 若要了解有关缩放脚本工作方式的详细信息, 请参阅[缩放脚本的工作](#how-the-scaling-script-works)原理部分。

## <a name="prerequisites"></a>先决条件

运行脚本的环境必须具有以下各项:

- 具有查询该租户的权限的 Windows 虚拟桌面租户和帐户或服务主体 (如 RDS 参与者)。
- 已配置 Windows 虚拟桌面服务并向其注册的会话主机池 Vm。
- 通过任务计划程序运行计划任务的其他虚拟机, 它具有对会话主机的网络访问权限。 稍后将在文档中将其称为 scaler VM。
- 运行计划任务的 VM 上安装的[Microsoft Azure 资源管理器 PowerShell 模块](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)。
- 运行计划任务的 VM 上安装的[Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)。

## <a name="recommendations-and-limitations"></a>建议和限制

运行缩放脚本时, 请记住以下事项:

- 此缩放脚本只能处理运行缩放脚本的计划任务的每个实例一个主机池。
- 运行缩放脚本的计划任务必须位于始终启用的 VM 上。
- 为缩放脚本及其配置的每个实例创建一个单独的文件夹。
- 此脚本不支持使用需要多重身份验证的 Azure AD 用户帐户以管理员身份登录 Windows 虚拟桌面。 建议使用服务主体访问 Windows 虚拟桌面服务和 Azure。 按照[此教程](create-service-principal-role-powershell.md)使用 PowerShell 创建服务主体和角色分配。
- Azure 的 SLA 保证仅适用于可用性集中的虚拟机。 文档的当前版本描述了一个环境, 其中的单个 VM 执行缩放, 这可能不符合可用性要求。

## <a name="deploy-the-scaling-script"></a>部署缩放脚本

以下过程将告诉你如何部署缩放脚本。

### <a name="prepare-your-environment-for-the-scaling-script"></a>为缩放脚本准备环境

首先, 为缩放脚本准备环境:

1. 登录到将使用域管理帐户运行计划任务的 VM (scaler VM)。
2. 在 scaler VM 上创建一个文件夹来保存缩放脚本及其配置 (例如, **\\C: HostPool1**)。
3. 下载**basicScale**和**Functions-PSStoredCredentials**文件,并从[扩展脚本存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script)下载**PowershellModules**文件夹, 并将其复制到在步骤2中创建的文件夹。 可以通过两种主要方式获取文件, 然后将其复制到 scaler VM:
    - 将 git 存储库克隆到本地计算机。
    - 查看每个文件的**原始**版本, 将每个文件的内容复制并粘贴到文本编辑器中, 然后使用相应的文件名和文件类型保存文件。 

### <a name="create-securely-stored-credentials"></a>创建安全存储的凭据

接下来, 需要创建安全存储的凭据:

1. 以管理员身份打开 PowerShell ISE。
2. 通过运行以下 cmdlet 导入 RDS PowerShell 模块:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. 打开编辑窗格并加载**Function-PSStoredCredentials**文件, 然后运行整个脚本 (F5)
4. 运行以下 cmdlet：
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    例如, **KeyPath-Scope 全局值 "c:\\HostPool1"** 。
5. 运行**StoredCredential-KeyPath \$KeyPath** cmdlet。 出现提示时, 输入具有查询主机池权限的 Windows 虚拟桌面凭据 (在**config.xml**中指定主机池)。
    - 如果使用不同的服务主体或标准帐户, 请对每个帐户运行**StoredCredential \$-KeyPath KeyPath** cmdlet 一次, 以创建本地存储的凭据。
6. 运行**StoredCredential-List**确认已成功创建凭据。

### <a name="configure-the-configxml-file"></a>配置 config.xml 文件

在以下字段中输入相关值, 以更新 config.xml 中的缩放脚本设置:

| 字段                     | 描述                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | 将会话主机 Vm 运行到的订阅关联的 Azure AD 租户 ID     |
| AADApplicationId              | 服务主体应用程序 ID                                                       |
| AADServicePrincipalSecret     | 这可以在测试阶段输入, 但一旦你通过 Functions-PSStoredCredentials 创建凭据, 就会将其保留为空 **。**    |
| currentAzureSubscriptionId    | 运行会话主机 Vm 的 Azure 订阅的 ID                        |
| tenantName                    | Windows 虚拟桌面租户名称                                                    |
| hostPoolName                  | Windows 虚拟桌面主机池名称                                                 |
| RDBroker                      | WVD 服务的 URL, 默认值 https:\//rdbroker.wvd.microsoft.com             |
| 用户名                      | 服务主体应用程序 ID (在 AADApplicationId 中可能具有相同的服务主体) 或没有多重身份验证的标准用户 |
| isServicePrincipal            | 接受的值为**true**或**false**。 指示所使用的第二组凭据是否为服务主体或标准帐户。 |
| BeginPeakTime                 | 当高峰使用时间开始时                                                            |
| EndPeakTime                   | 高峰使用时间结束时                                                              |
| TimeDifferenceInHours         | 本地时间和 UTC 之间的时间差 (以小时为单位)                                   |
| SessionThresholdPerCPU        | 每 CPU 的最大会话数阈值, 用于确定在高峰时段需要启动新的会话主机 VM 的时间。  |
| MinimumNumberOfRDSH           | 在非高峰使用时间保持运行的主机池 Vm 的最小数目             |
| LimitSecondsToForceLogOffUser | 强制用户注销前等待的秒数。如果设置为 0, 则不会强制用户注销。  |
| LogOffMessageTitle            | 强制注销前发送给用户的消息的标题                  |
| LogOffMessageBody             | 在用户注销前发送给用户的警告消息的正文。例如, "此计算机将在 X 分钟后关闭。 请保存你的工作并注销。 " |

### <a name="configure-the-task-scheduler"></a>配置任务计划程序

配置 config.xml 文件后, 需要将任务计划程序配置为按固定时间间隔运行 basicScaler 文件。

1. 开始**任务计划程序**。
2. 在 "**任务计划程序**" 窗口中, 选择 "**创建任务 ...** "
3. 在 **"创建任务**" 对话框中, 选择 "**常规**" 选项卡, 输入**名称**(例如, "动态 RDSH"), 选择 "**运行用户是否登录**", 或 "**使用最高权限运行**"。
4. 中转到 "**触发器**" 选项卡, 然后选择 "**新建 ...** "
5. 在 "**新建触发器**" 对话框中的 "**高级设置**" 下, 选中 "**重复任务**", 然后选择适当的时间段和持续时间 (例如**15 分钟**或**无限期**)。
6. 选择 "**操作**" 选项卡和 "**新建 ...** "
7. 在 "**新建操作**" 对话框中, 在 "**程序/脚本**" 字段中输入 " **powershell** ", **\\\\** 然后在 "添加参数 (可选)" 字段中输入 C: basicScale。
8. 前往 "**条件**和**设置**" 选项卡, 然后选择 **"确定"** 以接受每个选项的默认设置。
9. 输入计划运行缩放脚本的管理帐户的密码。

## <a name="how-the-scaling-script-works"></a>缩放脚本的工作原理

此缩放脚本从 config.xml 文件中读取设置, 包括一天中高峰使用期限的开始和结束时间。

高峰使用时间期间, 该脚本会检查当前会话数和每个主机池的当前正在运行的 RDSH 容量。 它计算正在运行的会话主机 Vm 是否有足够的容量来支持基于 config.xml 文件中定义的 SessionThresholdPerCPU 参数的现有会话。 否则, 该脚本会在主机池中启动其他会话主机 Vm。

在非高峰使用时间, 该脚本根据 config.xml 文件中的 MinimumNumberOfRDSH 参数确定应关闭哪些会话主机 Vm。 此脚本会将会话主机 Vm 设置为排出模式, 以防新会话连接到主机。 如果将 config.xml 文件中的**LimitSecondsToForceLogOffUser**参数设置为非零正值, 则该脚本将通知所有当前已登录的用户保存工作, 等待配置的时间长度, 然后强制用户注销。在会话主机 VM 上注销所有用户会话后, 该脚本将关闭服务器。

如果将 config.xml 文件中的**LimitSecondsToForceLogOffUser**参数设置为零, 则该脚本将允许主机池属性中的会话配置设置处理注销用户会话。 如果会话主机 VM 上有任何会话, 它会使会话主机 VM 运行。 如果没有任何会话, 该脚本将关闭会话主机 VM。

此脚本设计为在 scaler VM 服务器上使用任务计划程序定期运行。 根据远程桌面服务环境的大小选择适当的时间间隔, 并记住启动和关闭虚拟机可能需要一些时间。 建议每15分钟运行一次缩放脚本。

## <a name="log-files"></a>日志文件

缩放脚本创建两个日志文件: **WVDTenantScale**和**WVDTenantUsage**。 **WVDTenantScale**文件将记录每次执行缩放脚本时的事件和错误 (如果有)。

每次执行缩放脚本时, **WVDTenantUsage**文件都将记录活动的内核数和活动数量的虚拟机。 你可以使用此信息来估算 Microsoft Azure Vm 的实际使用情况和成本。 此文件的格式为逗号分隔值, 每个项都包含以下信息:

>时间, 主机池, 个内核, 个 Vm

还可以修改文件名以使其具有 .csv 扩展名, 并将其加载到 Microsoft Excel 中并进行分析。
