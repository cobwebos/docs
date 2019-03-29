---
title: 安装和部署 Linux C 代理的 ASC IoT 代理预览版指南 |Microsoft Docs
description: 了解如何在 32 位和 64 位 Linux 上安装 IoT 代理 ASC。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 2b18a09e5b79e7b3d3ea837e937397ac92491f9f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619843"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>为适用于 Linux 的 IoT C 基于安全代理部署 ASC

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南介绍如何安装和部署 Linux 上的 IoT C 基于安全代理为 ASC。

本指南介绍如何： 
> [!div class="checklist"]
> * 安装
> * 验证部署
> * 卸载代理
> * 故障排除 

## <a name="prerequisites"></a>必备组件

有关其他平台和代理版本，请参阅[选择适当的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全代理，你希望 (sudo) 安装在计算机上需要本地管理员权限。

1. [创建安全模块](quickstart-create-security-twin.md)设备。

## <a name="installation"></a>安装 

若要安装和部署安全代理，请执行以下操作：


1. 最新版本下载到您的计算机与[Github](https://aka.ms/iot-security-github-c)。

1. 提取包的内容，并导航到 _/安装_文件夹。

1. 正在运行将权限添加到**InstallSecurityAgent 脚本**运行以下命令：
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 接下来，运行： 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)有关身份验证参数的详细信息。

此脚本执行以下任务：

1. 安装先决条件。

2. 添加服务用户 （使用交互式登录名已禁用）。

3. 安装代理作为**守护程序**-假定设备使用**systemd**服务管理。

4. 使用提供的身份验证参数来配置代理。 

有关其他帮助，运行该脚本使用 – help 参数： 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，请运行脚本时使用 –-uninstall 参数：

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>故障排除
运行检查部署状态：

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>后续步骤
- 阅读 IoT 服务 ASC[概述](overview.md)
- 要详细了解 ASC 的 IoT[体系结构](architecture.md)
- 启用[服务](quickstart-onboard-iot-hub.md)
- 读取[常见问题](resources-frequently-asked-questions.md)
- 了解[安全警报](concept-security-alerts.md)