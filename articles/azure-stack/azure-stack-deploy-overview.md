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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: bf07fe56c65e53f5485b9927e0d704f80842cf3a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338688"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>快速入门：评估 Azure Stack 开发工具包

[Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-what-is.md) 是一个测试和开发环境，可以在部署后用来评估和演示 Azure Stack 功能和服务。 若要开始使用 ASDK，需先准备主机硬件，然后运行一些脚本（安装可能需要数小时）。 之后便可以登录到管理员门户或用户门户，开始使用 Azure Stack。

## <a name="prerequisites"></a>必备组件

### <a name="asdk-host-computer-requirements"></a>ASDK 主机要求

在安装 ASDK 之前，需准备用于托管开发工具包的计算机。 开发工具包主机必须满足**[查看 ASDK 部署规划注意事项](.\asdk\asdk-deploy-considerations.md)** 中所述的硬件、软件和网络要求。

> [!TIP]
> 在开发工具包主机上安装操作系统以后，可以使用 [Azure Stack 部署要求检查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)来确认硬件是否满足所有要求。

### <a name="account-requirements"></a>帐户要求

还需选择是使用 Azure Active Directory (Azure AD) 还是 Active Directory 联合身份验证服务 (AD FS) 作为部署的标识解决方案。 查看**[部署规划注意事项](.\asdk\asdk-deploy-considerations.md#account-requirements)** 中的帐户要求

## <a name="download-and-extract-the-deployment-package"></a>下载并提取部署包

准备好开发工具包主机以后，请下载并提取 ASDK 部署包。 部署包包括 Cloudbuilder.vhdx 文件，该文件是一个虚拟硬盘驱动器 (VHD)，其中包括一个可启动的操作系统，以及 Azure Stack 安装文件。

可以将部署包下载到开发工具包主机或另一台计算机。 提取的部署文件占用 60 GB 的可用磁盘空间，因此使用另一台计算机有助于减少开发工具包主机上的存储要求。

**[下载并提取 Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>准备主机

必须先准备主机环境并将系统配置为从开发工具包 VHD 启动，然后才能安装 ASDK。 重启主机时，主机会从 CloudBuilder.vhdx 启动，然后即可开始部署 ASDK。

**[准备 ASDK 主机](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>在主机上安装 ASDK

主机从 VHD 启动以后，即可将开发工具包部署到 Cloudbuilder 虚拟环境。 可以使用图形用户界面 (GUI) 部署 ASDK，后者可以通过运行 asdk-installer.ps1 PowerShell 脚本获得，也可以从 [PowerShell 命令行](.\asdk\asdk-deploy-powershell.md)获得。

> [!NOTE]
> 主机从 Cloudbuilder.vhdx 映像启动以后，即可配置 [Azure Stack 遥测设置](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)，然后安装 ASDK。

**[安装 Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>执行部署后配置

安装 ASDK 之后，建议进行一些安装后检查和配置更改。

**工具**

安装 Azure Stack PowerShell 和 GitHub 工具，然后使用 test-AzureStack cmdlet 检查安装是否成功。

**标识解决方案**

对于使用 Azure AD 的部署，必须激活 Azure Stack 管理员门户和租户门户。 对于目录的所有用户来说，此激活是指同意为 Azure Stack 门户和 Azure 资源管理器提供正确的权限（已在同意页上列出）。

**密码过期**

应该重置密码过期策略，确保开发工具包主机的密码不在评估期结束之前过期。

> [!NOTE]
> 也可在安装 ASDK 之后配置 [Azure Stack 遥测设置](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)。

**[ASDK 后部署任务](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>注册到 Azure

必须将 Azure Stack 注册到 Azure，以便[将 Azure 市场项下载](.\asdk\asdk-marketplace-item.md)到 Azure Stack。

**[将 Azure Stack 注册到 Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>后续步骤

祝贺你！ 完成本快速入门中的步骤以后，你就有了一个包含[管理员](https://adminportal.local.azurestack.external)门户和[用户](https://portal.local.azurestack.external)门户的 ASDK 环境。
