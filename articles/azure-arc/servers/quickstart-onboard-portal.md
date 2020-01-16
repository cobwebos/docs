---
title: 快速入门：使用用于服务器的 Azure Arc 将计算机连接到 Azure - 门户
description: 本快速入门介绍如何在门户中使用用于服务器的 Azure Arc 将计算机连接到 Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: azure 自动化、DSC、powershell、所需状态配置、更新管理、更改跟踪、库存、runbook、python、图形、混合、载入
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 26c79db956b2703bf037fc6f7790d4ee13874410
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834249"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>快速入门：使用用于服务器的 Azure Arc 将计算机连接到 Azure - 门户

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

在[用于服务器的 Azure Arc 概述](overview.md)中查看受支持的客户端和所需的网络配置。

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>使用 Azure 门户生成代理安装脚本

1. 启动 [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. 单击“+添加” 
1. 按照向导完成操作
1. 最后一页包含一个生成的脚本，可以复制（或下载）该脚本。

该脚本必须在要连接的目标计算机上运行。 该脚本通过一个操作下载并安装代理，然后连接计算机。

在要管理的非 Azure 服务器上：

1. 登录到服务器（使用 SSH、RDP 或 PowerShell 远程处理）
1. 启动 shell：在 Linux 上启动 bash，在 Windows 上以管理员身份启动 PowerShell
1. 粘贴门户中提供的脚本，并在要连接到 Azure 的服务器上执行该脚本。
1. 用于加入单个服务器的默认身份验证是使用 Azure“设备登录”的交互式身份验证。  运行该脚本时，将看到如下所示的消息：

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   打开浏览器并输入该代码以进行身份验证。 浏览器无需在要加入的服务器上运行，它可以在其他计算机（例如笔记本电脑）上运行。

1. 若要进行非交互式身份验证，请遵循[创建服务主体](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale)中的步骤，并修改门户中生成的脚本。

> [!NOTE]
> 如果在服务器上使用 Internet Explorer 进行首次登录，将会出现错误。只需重新打开浏览器并再次登录即可。

## <a name="execute-the-script-on-target-nodes"></a>在目标节点上执行脚本

登录到每个节点，并执行门户中生成的脚本。 脚本成功完成运行后，请转到 Azure 门户，验证是否已成功连接服务器。

![成功加入](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>清除

若要从用于服务器的 Azure Arc 断开连接计算机，需要执行两个步骤。

1. 在[门户](https://aka.ms/hybridmachineportal)中选择该计算机，单击省略号 (`...`)，然后选择“删除”  。
1. 从计算机中卸载代理。

   在 Windows 上，可以使用“应用和功能”控制面板来卸载代理。
  
  ![应用和功能](./media/quickstart-onboard/apps-and-features.png)

   若要编写卸载脚本，可以参考以下示例，该示例将检索 **PackageId** 并使用 `msiexec /X` 卸载代理。

   查看注册表项 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 并找到 **PackageId**。 然后，可以使用 `msiexec` 卸载代理。

   以下示例演示如何卸载代理。

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   在 Linux 上，请执行以下命令来卸载代理。

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将策略分配到连接的计算机](../../governance/policy/assign-policy-portal.md)
