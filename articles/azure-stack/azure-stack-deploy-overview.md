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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235179"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>快速入门： 评估 Azure 堆栈开发工具包

[Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-what-is.md) 是一个测试和开发环境，可以在部署后用来评估和演示 Azure Stack 功能和服务。 若要开始使用 ASDK，需先准备主机硬件，然后运行一些脚本（安装可能需要数小时）。 之后，你可以登录到管理员或用户门户若要开始使用 Azure 堆栈。

## <a name="prerequisites"></a>必备组件

### <a name="asdk-host-computer-requirements"></a>ASDK 主机计算机要求

在安装之前 ASDK，需要准备将承载开发工具包的计算机。 开发工具包主机计算机必须满足硬件、 软件和网络要求中所述**[查看 ASDK 部署规划注意事项](.\asdk\asdk-deploy-considerations.md)**。

> [!TIP]
> 在开发工具包主机上安装操作系统以后，可以使用 [Azure Stack 部署要求检查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)来确认硬件是否满足所有要求。

### <a name="account-requirements"></a>帐户要求

还需选择是使用 Azure Active Directory (Azure AD) 还是 Active Directory 联合身份验证服务 (AD FS) 作为部署的标识解决方案。 查看中的帐户要求**[部署规划注意事项](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>下载并提取部署包

准备你的开发工具包主机计算机之后, 下载并提取 ASDK 部署包。 部署包包括可启动的操作系统虚拟硬盘 (VHD) 的 Cloudbuilder.vhdx 文件和 Azure 堆栈安装文件。

可以将部署包下载到开发工具包主机或另一台计算机。 提取的部署文件需要 60 GB 的可用磁盘空间，以便使用另一台计算机可以帮助减少开发工具包主机上的存储要求。

**[下载并提取 Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>准备主机

你可以安装 ASDK 之前，必须准备主机环境和系统配置为启动从开发工具包 VHD。 重新启动主机时，它从 CloudBuilder.vhdx 启动，并可以开始部署 ASDK。

**[准备 ASDK 主机](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>在主机上安装 ASDK

从 VHD 启动主机后，你可以部署到 Cloudbuilder 虚拟环境开发工具包。 你可以部署使用图形用户界面 (GUI)，提供通过运行 asdk installer.ps1 PowerShell 脚本，或从 ASDK [PowerShell 命令行](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> 从 Cloudbuilder.vhdx 映像启动主机后，你可以选择配置[Azure 堆栈遥测设置](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)*之前*安装 ASDK。

**[安装 Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>执行部署后配置

安装 ASDK 之后，建议进行一些安装后检查和配置更改。

**工具**

安装 Azure 堆栈 PowerShell 和 GitHub 工具，并检查你使用测试 AzureStack cmdlet 的安装成功。

**标识解决方案**

对于使用 Azure AD 部署，你必须激活 Azure 堆栈管理员和租户门户。 对于目录的所有用户来说，此激活是指同意为 Azure Stack 门户和 Azure 资源管理器提供正确的权限（已在同意页上列出）。

**密码过期**

你应该重置以确保开发工具包宿主的密码不会评估期结束前过期的密码过期策略。

> [!NOTE]
> 你还可以配置的选项[Azure 堆栈遥测设置](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)*后*安装 ASDK。

**[ASDK 后部署任务](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>注册到 Azure

必须将 Azure Stack 注册到 Azure，以便[将 Azure Marketplace 项下载](.\asdk\asdk-marketplace-item.md)到 Azure Stack。

**[将 Azure Stack 注册到 Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>后续步骤

祝贺你！ 通过完成本快速入门教程中的步骤中，你可以具有的 ASDK 环境[管理员](https://adminportal.local.azurestack.external)门户和[用户](https://portal.local.azurestack.external)门户。
