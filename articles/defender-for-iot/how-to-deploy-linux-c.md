---
title: 安装 & 部署 Linux C 代理
description: 了解如何在 Linux 上安装和部署基于 IoT C 的安全代理 Defender
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8c03f6b882c8b1a64c9f256493c5d586b5fa0f89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934661"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>为 Linux 部署基于 IoT C 的安全代理 Defender

本指南介绍了如何在 Linux 上安装和部署基于 IoT C 的安全代理的 Defender。

本指南介绍如何：

> [!div class="checklist"]
> * 安装
> * 验证部署
> * 卸载代理
> * 疑难解答

## <a name="prerequisites"></a>先决条件

有关其他平台和代理风格，请参阅 [选择正确的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全代理，需要在 (sudo) 上安装的计算机上具有本地管理员权限。

1. 为设备[创建安全模块](quickstart-create-security-twin.md)。

## <a name="installation"></a>安装

若要安装和部署安全代理，请使用以下工作流：

1. 从 [GitHub](https://aka.ms/iot-security-github-c)下载最新版本到您的计算机。

1. 提取包的内容，然后导航到 _/src/installation_ 文件夹。

1. 通过运行以下命令将运行权限添加到 **InstallSecurityAgent 脚本** ：

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 然后运行：

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   有关身份验证参数的详细信息，请参阅[如何配置身份验证](concept-security-agent-authentication-methods.md)。

此脚本执行以下功能：

1. 安装必备组件。

1. 添加已禁用交互式登录) 的服务用户 (。

1. 安装用作**守护程序**的代理 - 假设设备使用 **systemd** 进行服务管理。

1. 使用提供的身份验证参数配置代理。

如需更多帮助，请结合 -help 参数运行该脚本：

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>卸载代理

若要卸载代理，请运行包含–-uninstall 参数的脚本：

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>疑难解答

运行以下命令来检查部署状态：

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>后续步骤

- 阅读用于 IoT 服务的 Defender [概述](overview.md)
- 了解有关用于 IoT[体系结构](architecture.md)的 Defender 的详细信息
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读[常见问题解答](resources-frequently-asked-questions.md)
- 了解 [安全警报](concept-security-alerts.md)
