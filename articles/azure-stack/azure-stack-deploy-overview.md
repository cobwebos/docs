---
title: 评估 Azure Stack 开发工具包 | Microsoft Docs
description: 了解如何部署用于评估目的的 Azure Stack 开发工具包。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>快速入门：评估 Azure Stack 开发工具包
[Azure 堆栈开发工具包 (ASDK)](.\asdk\asdk-what-is.md)是一个测试和开发环境，你可以部署评估并演示 Azure 堆栈功能和服务。 若要开始使用 ASDK，需要准备计算机硬件的主机，然后运行一些脚本 （安装花几个小时）。 之后，你可以登录到管理员和用户门户若要开始使用 Azure 堆栈。

## <a name="prerequisites"></a>必备组件 
在安装之前 ASDK，需要准备将承载开发工具包 （开发工具包主机） 的计算机。 开发工具包主机计算机必须满足最低硬件、 软件和网络要求。 

你还需要选择是要使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识解决方案为你的部署。 

请确保开发工具包主机满足最低硬件要求以及你已启动你的部署，以便顺利运行安装过程之前，使你标识解决方案决策。 

**[查看 ASDK 部署规划注意事项](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> 你可以使用[Azure 堆栈部署要求检查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)以确认你的硬件满足所有要求在开发工具包主机计算机上安装操作系统后。

## <a name="download-and-extract-the-deployment-package"></a>下载并提取部署包
确保你开发工具包的主机计算机满足安装 ASDK 的基本要求之后下, 一步是要下载并解压缩 ASDK 部署包。 部署包包括 Cloudbuilder.vhdx 文件，它是虚拟硬盘，其中包括可启动的操作系统和 Azure 堆栈安装文件。

你可以将部署包下载到开发工具包主机或另一台计算机。 提取的部署文件占用 60 GB 的可用磁盘空间，因此使用另一台计算机有助于减少开发工具包主机的硬件要求。

**[下载并提取 Azure 堆栈开发工具包 (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>准备开发工具包主机计算机
你可以在主计算机上安装 ASDK 之前，必须准备环境和系统配置为启动从 VHD。 已准备好开发工具包主机计算机后，它从其启动 CloudBuilder.vhdx 虚拟机硬盘空间，以便可以开始 ASDK 部署。

**[准备 ASDK 主计算机](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>在主计算机上安装 ASDK
在准备好开发工具包主机计算机后, ASDK 可以部署到 CloudBuilder.vhdx 映像。 可以使用提供通过下载并运行 asdk installer.ps1 PowerShell 脚本或者完全从提供的图形用户界面 (GUI) 部署 ASDK[命令行](.\asdk\asdk-deploy-powershell.md)。 

> [!NOTE]
> （可选） 的主机计算机引导到 CloudBuilder.vhdx 之后，你可以配置[Azure 堆栈遥测设置](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)*之前*安装 ASDK。


**[安装 Azure 堆栈开发工具包 (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>执行后期部署配置
安装后 ASDK，有几个建议的安装后检查和应做的配置更改。 你可以验证你的安装已成功使用测试 AzureStack cmdlet 中，安装并安装 Azure 堆栈 PowerShell 和 GitHub 工具。 

在完成使用 Azure AD 的部署以后，必须激活 Azure Stack 管理员门户和租户门户。 对于目录的所有用户来说，此激活是指同意为 Azure Stack 门户和 Azure 资源管理器提供正确的权限（已在同意页上列出）。

你应该重置以确保开发工具包宿主的密码不会评估期结束前过期的密码过期策略。

> [!NOTE]
> （可选） 还可以配置[Azure 堆栈遥测设置](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)*后*安装 ASDK。

**[Post ASDK 部署任务](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>注册 Azure
你必须向 Azure 注册 Azure 堆栈，以便您能够[下载 Azure 应用商店项](.\asdk\asdk-marketplace-item.md)到 Azure 堆栈。

**[向 Azure 注册 Azure 堆栈](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>后续步骤
祝贺你！ 完成这些步骤后，你将有两种版本同时开发工具包环境[管理员](https://adminportal.local.azurestack.external)和[用户](https://portal.local.azurestack.external)门户。 
