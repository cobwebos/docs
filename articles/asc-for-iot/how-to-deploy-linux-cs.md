---
title: 安装和部署 Linux 指南C#代理的 IoT 预览版 ASC |Microsoft Docs
description: 了解如何在 32 位和 64 位 Linux 上安装 IoT 代理 ASC。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: d6b4e6065b0ef198ad583b3760124730e658fe0b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619902"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>为 IoT 部署 ASC C#-基于 Linux 的安全代理

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南介绍如何安装和部署 IoT ASC C#-基于 Linux 的安全代理。

本指南介绍如何： 
> [!div class="checklist"]
> * 安装
> * 验证部署
> * 卸载代理
> * 故障排除 

## <a name="prerequisites"></a>必备组件

有关其他平台和代理版本，请参阅[选择适当的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全代理，你想要安装在计算机上需要本地管理员权限。 

1. [创建安全模块](quickstart-create-security-twin.md)设备。

## <a name="installation"></a>安装 

若要部署安全代理，请执行以下操作：

1. 最新版本下载到您的计算机与[Github](https://aka.ms/iot-security-github-cs)。

1. 提取包的内容，并导航到 _/安装_文件夹。

1. 正在运行将权限添加到**InstallSecurityAgent 脚本**通过运行 `chmod +x InstallSecurityAgent.sh` 

1. 接下来，运行： 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)有关身份验证参数的详细信息。

此脚本执行以下任务：

- 安装先决条件。

- 添加服务用户 （使用交互式登录名已禁用）。

- 安装代理作为**守护程序**-这将假定设备使用**systemd**服务管理。

- 配置**sudoers**允许代理以 root 身份执行某些任务。

- 使用提供的身份验证参数来配置代理。


有关其他帮助，运行该脚本使用 – help 参数： `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，使用-u 参数运行脚本： `./InstallSecurityAgent.sh -u`。 

> [!NOTE]
> 卸载不会删除在安装过程中安装任何缺少的必备组件。

## <a name="troubleshooting"></a>故障排除  

1. 运行检查部署状态：

    `systemctl status ASCIoTAgent.service`

2. 启用日志记录。  
   如果代理无法启动，启用日志记录以获取详细信息。

   启用的日志记录：

   1. 打开在任何 Linux 编辑器中编辑的配置文件：

        `vi /var/ASCIoTAgent/General.config`

   1. 编辑以下值： 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       **LogFilePath**是可配置的值。 

       > [!NOTE]
       > 建议启用日志记录**关闭**故障排除完毕后。 保留日志记录**上**增加日志文件大小和数据使用情况。

   1. 通过运行重新启动该代理：

       `systemctl restart ASCIoTAgent.service`

   1. 查看有关失败的详细信息的日志文件。  

       日志文件的位置是： `/var/ASCIoTAgent/IotAgentLog.log`

       更改文件位置路径为选择的名称根据**logFilePath**步骤 2 中。 

## <a name="next-steps"></a>后续步骤

- 阅读 IoT 服务 ASC[概述](overview.md)
- 要详细了解 ASC 的 IoT[体系结构](architecture.md)
- 启用[服务](quickstart-onboard-iot-hub.md)
- 读取[常见问题](resources-frequently-asked-questions.md)
- 了解[警报](concept-security-alerts.md)