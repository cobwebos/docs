---
title: 安装 & 部署 Linux C 代理
description: 了解如何在32位和64位 Linux 上安装适用于 IoT 代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311193"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>为 Linux 部署适用于 IoT 的 Azure 安全中心基于 C 的安全代理

本指南介绍了如何在 Linux 上安装和部署适用于基于 IoT C 的安全代理的 Azure 安全中心。

本指南介绍如何：

> [!div class="checklist"]
> * 安装
> * 验证部署
> * 卸载代理
> * 疑难解答

## <a name="prerequisites"></a>先决条件

有关其他平台和代理风格，请参阅[选择正确的安全代理](how-to-deploy-agent.md)。

1. 若要部署安全代理，需要在要安装的计算机（sudo）上进行本地管理员权限。

1. 为设备[创建安全模块](quickstart-create-security-twin.md)。

## <a name="installation"></a>安装

若要安装和部署安全代理，请使用以下工作流：

1. 从[GitHub](https://aka.ms/iot-security-github-c)下载最新版本到您的计算机。

1. 提取包的内容，然后导航到 _/src/installation_文件夹。

1. 通过运行以下命令将运行权限添加到**InstallSecurityAgent 脚本**：

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

1. 添加服务用户（禁用交互式登录）。

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

- 阅读 Azure 安全中心以获取 IoT 服务[概述](overview.md)
- 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读[常见问题解答](resources-frequently-asked-questions.md)
- 了解[安全警报](concept-security-alerts.md)
