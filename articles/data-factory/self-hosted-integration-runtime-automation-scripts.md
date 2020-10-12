---
title: 使用本地 PowerShell 脚本自动安装自承载集成运行时
description: 在本地计算机上自动安装自承载集成运行时。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 068586a96ad3655cb70171266bd58f56ed320fc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83662855"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>使用本地 PowerShell 脚本自动安装自承载集成运行时
若要在本地计算机（而不是在可以通过资源管理器模板实现自动安装的 Azure VM 上）上自动安装自承载集成运行时，可以使用本地 PowerShell 脚本。 本文将介绍两个可用于实现自动安装的脚本。

## <a name="prerequisites"></a>先决条件

* 在本地计算机上启动 PowerShell。 若要运行脚本，需要选择“以管理员身份运行”。
* [下载](https://www.microsoft.com/download/details.aspx?id=39717)自承载集成运行时软件。 复制下载的文件所在的路径。 
* 还需要“身份验证密钥”来注册自承载集成运行时。
* 若要自动执行手动更新，需要具备一个预先配置的自承载集成运行时。

## <a name="scripts-introduction"></a>脚本简介 

> [!NOTE]
> 以下脚本通过自承载集成运行时中[记录的命令行实用工具](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#set-up-an-existing-self-hosted-ir-via-local-powershell)创建。 如果需要，可以相应地自定义这些脚本以满足其自动化需求。
> 脚本需要应用于每个节点，因此确保在高可用性设置（2 个或更多节点）的情况下跨所有节点运行脚本。

* 自动安装：使用 [InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1) 安装并注册新的自承载集成运行时节点 - 该脚本可用于安装自承载集成运行时节点，并使用身份验证密钥对其进行注册。 脚本接受两个参数，第一个指定本地磁盘上的[自承载集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)的位置，第二个指定身份验证密钥（用于注册自承载 IR 节点）  。

* 自动执行手动更新：使用 [script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1) 特定版本或最新版本更新自承载 IR 节点 - 如果你已关闭自动更新或需要对更新有更多的控制，此方法也同样受支持。 该脚本可用于将自承载集成运行时节点更新到最新版本或指定的更高版本（无法降级）。 它接受用于指定版本号的参数（例如 -version 3.13.6942.1）。 未指定版本时，它始终将自承载 IR 更新到[下载页面](https://www.microsoft.com/download/details.aspx?id=39717)中可用的最新版本。
    > [!NOTE]
    > 只能指定上 3 个版本。 最好使用此脚本将现有节点更新到最新版本。 本脚本假设你已有一个经过注册的自承载 IR。 

## <a name="usage-examples"></a>用法示例

### <a name="for-automating-setup"></a>自动安装
1. 从[此处](https://www.microsoft.com/download/details.aspx?id=39717)下载自承载 IR。 
1. 指定上述下载的 SHIR MSI（安装文件）所在的路径。 例如，如果路径为 C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi，则可以使用下面的 PowerShell 命令行示例执行此任务：

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > 将 [key] 替换为用以注册 IR 的身份验证密钥。
    > 将“username”替换为你的用户名。
    > 运行此脚本时，指定“InstallGatewayOnLocalMachine.ps1”文件的位置。 在本例中，我们将其存储在桌面上。

1. 如果计算机上有一个预安装的自承载 IR，此脚本自动将其卸载，然后配置一个新的自承载 IR。 系统会弹出以下窗口：![配置集成运行时](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. 安装和密钥注册完成后，PowerShell 中会显示“网关安装成功”和“网关注册成功”结果 。
        [![脚本 1 运行结果](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>自动执行手动更新
此脚本用于更新/安装和注册最新的自承载集成运行时。 该脚本运行执行以下步骤：
1. 检查当前自承载 IR 版本
2. 从参数获取最新版本或指定版本
3. 如果存在比当前版本更新的版本：
    * 下载自承载 IR msi
    * 执行升级

可以按照以下命令行示例使用此脚本：
* 下载并安装最新版网关：

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* 下载并安装指定版本的网关：
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   如果当前版本已经是最新版本，系统会显示以下结果，表明不需要更新。   
    [![脚本 2 运行结果](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)
