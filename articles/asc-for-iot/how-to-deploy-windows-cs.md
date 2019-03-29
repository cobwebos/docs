---
title: Windows 安装的 ASC IoT 代理预览版 |Microsoft Docs
description: 了解有关如何安装适用于 32 位或 64 位 Windows 设备上 IoT 代理 ASC。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621821"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>为 IoT 部署 ASC C#-基于 Windows 的安全代理

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南介绍如何安装适用于 IoT ASC C#-基于 Windows 上的安全代理。

本指南介绍如何： 
> [!div class="checklist"]
> * 安装
> * 验证部署
> * 卸载代理
> * 故障排除 

## <a name="prerequisites"></a>必备组件

有关其他平台和代理风格，请参阅[选择适当的安全代理](how-to-deploy-agent.md)。

1. 你想要安装在计算机上的本地管理员权限。 

1. [创建安全模块](quickstart-create-security-twin.md)设备。

## <a name="installation"></a>安装 

若要安装安全代理，请执行以下操作：

1. 若要安装适用于 IoT Windows ASCC#代理在设备上，下载最新版本为您的计算机与 ASC iot [GitHub 存储库](https://github.com/Azure/Azure-IoT-Security-Agent-CS)。

2. 提取包的内容，并导航到 /Install 文件夹。

3. 以管理员身份打开 Windows PowerShell。 
    1. 通过运行正在运行的权限添加到 InstallSecurityAgent 脚本 ```Unblock-File .\InstallSecurityAgent.ps1```
    
        并运行：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    例如：
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)有关身份验证参数的详细信息。

此脚本执行以下任务：

- 安装先决条件。

- 添加服务用户 （使用交互式登录名已禁用）。

- 安装代理作为**系统服务**。

- 使用提供的身份验证参数来配置代理。


有关其他帮助，请在 PowerShell 中使用的 Get-help 命令 <br>获取帮助的示例：  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>验证部署状态

- 运行检查代理部署状态：<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理：

1. 运行以下 PowerShell 脚本 **-模式**参数设置为**卸载**。  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>故障排除

如果代理无法启动，启用日志记录 (日志记录*关闭*默认情况下) 以获取详细信息。

若要启用日志记录：

1. 打开配置文件 (General.config) 进行编辑使用标准文件编辑器。

1. 编辑以下值：

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 建议启用日志记录**关闭**故障排除完毕后。 保留日志记录**上**增加日志文件大小和数据使用情况。 

1. 通过运行以下 PowerShell 或命令行重新启动该代理：

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

1. 查看有关失败的详细信息的日志文件。

   日志文件位置： `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>后续步骤
- 阅读 IoT 服务 ASC[概述](overview.md)
- 要详细了解 ASC 的 IoT[体系结构](architecture.md)
- 启用[服务](quickstart-onboard-iot-hub.md)
- 读取[常见问题](resources-frequently-asked-questions.md)
- 了解[警报](concept-security-alerts.md)