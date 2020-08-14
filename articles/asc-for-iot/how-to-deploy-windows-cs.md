---
title: '在 Windows 设备上安装 c # 代理'
description: 了解如何在32位或64位 Windows 设备上安装用于 IoT 代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 64b9176a16f4d8d2b7ee7f33e00714d811c5f070
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209078"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>为 Windows 部署适用于 IoT 的 Azure 安全中心基于 C# 的安全代理

本指南说明如何在 Windows 上安装 Azure 安全中心基于 IoT c # 的安全代理。

本指南介绍如何：

> [!div class="checklist"]
> * 安装
> * 验证部署
> * 卸载代理
> * 疑难解答

## <a name="prerequisites"></a>先决条件

有关其他平台和代理风格，请参阅 [选择正确的安全代理](how-to-deploy-agent.md)。

1. 要在其上安装的计算机上的本地管理员权限。

1. 为设备[创建安全模块](quickstart-create-security-twin.md)。

## <a name="installation"></a>安装

若要安装安全代理，请使用以下工作流：

1. 在设备上安装适用于 IoT Windows c # 代理的 Azure 安全中心。 从适用于 IoT [GitHub 存储库](https://github.com/Azure/Azure-IoT-Security-Agent-CS)的 Azure 安全中心将最新版本下载到你的计算机。

1. 提取包的内容，并导航到 /Install 文件夹。

1. 以管理员身份打开 Windows PowerShell。
1. 通过运行以下内容将运行权限添加到 InstallSecurityAgent 脚本：

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    然后运行：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    例如：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    有关身份验证参数的详细信息，请参阅 [如何配置身份验证](concept-security-agent-authentication-methods.md)。

此脚本执行以下操作：

* 安装必备组件。
* 添加已禁用交互式登录) 的服务用户 (。
* 将代理作为**系统服务**安装。
* 使用提供的身份验证参数配置代理。

有关更多帮助，请在 PowerShell 中使用 Get-help 命令。

Get-help 示例：    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>验证部署状态

运行以下命令来检查代理部署状态：

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，请执行以下操作：

1. 运行以下 PowerShell 脚本，将 **-mode** 参数设置为 **Uninstall**。

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>故障排除

如果代理无法启动，请启用日志记录（日志记录默认设置为“禁用”**）以获取更多信息。

若要启用日志记录，请执行以下操作：

1. 使用标准文件编辑器打开 ( # A0) 的配置文件进行编辑。

1. 编辑以下值：

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 建议在故障排除操作完成后关闭日志记录功能。**** 让日志记录保持启用状态会增加日志文件大小和数据使用量。****

1. 运行以下 PowerShell 或命令行，以便重启代理：

    **Powershell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   或

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. 查看日志文件，了解有关故障的详细信息。 日志文件将出现在运行脚本的工作目录中。 

   日志文件位置：`.\IoTAgentLog.log`

## <a name="next-steps"></a>后续步骤

* 阅读 Azure 安全中心以获取 IoT 服务 [概述](overview.md)
* 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
* 启用该[服务](quickstart-onboard-iot-hub.md)
* 阅读[常见问题解答](resources-frequently-asked-questions.md)
* 了解[警报](concept-security-alerts.md)
