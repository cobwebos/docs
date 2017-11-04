---
title: "Azure 堆栈开发工具包部署快速入门 |Microsoft 文档"
description: "了解如何部署 Azure 堆栈开发工具包"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 3d3f31775ae9384faeae20f108b71cea5dcc2934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-development-kit-deployment-quickstart"></a>Azure Stack 开发工具包部署快速入门

*适用范围： Azure 堆栈开发工具包*

[Azure 堆栈开发工具包](azure-stack-poc.md)是一个测试和开发环境，你可以部署评估并演示 Azure 堆栈功能和服务。 若要获取其启动并正在运行，你将需要准备环境硬件并运行 （这将需要几个小时） 一些脚本。 之后，你将可以登录到管理员门户和用户门户管理 Azure 堆栈和测试产品/服务。 

1. [**规划你的硬件、 软件和网络**](azure-stack-deploy.md)。 承载开发工具包 （开发工具包主机） 的计算机必须满足硬件、 软件和网络要求。 你还必须使用 Azure Active Directory 或 Active Directory 联合身份验证服务之间进行选择。 请务必在开始你的部署，以便顺利运行安装过程之前符合这些先决条件。 

2. [**下载并提取部署包**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit)。 你可以将部署包下载到开发工具包主机或另一台计算机。 提取的部署文件需要 60 GB 的可用磁盘空间，以便使用另一台计算机可以帮助减少开发工具包主机的硬件要求。

3. [**准备开发工具包主机**](azure-stack-run-powershell-script.md#prepare-the-development-kit-host)使用安装程序。 此步骤后，开发工具包主机将启动到 Cloudbuilder.vhdx （包括可启动的操作系统和 Azure 堆栈的虚拟硬盘驱动器安装文件）。

4. [**部署开发工具包**](azure-stack-run-powershell-script.md#deploy-the-development-kit)开发工具包主机上。

5. 如果你的 Azure 堆栈部署使用 Azure Active Directory，则必须[向 Azure 注册 Azure 堆栈](azure-stack-register.md)，以便你可以[下载 Azure 应用商店项](azure-stack-download-azure-marketplace-item.md)到 Azure 堆栈。

完成这些步骤后，你必须具有管理员和用户门户的开发工具包环境。 接下来，你可以[连接和登录](azure-stack-connect-azure-stack.md)到门户。 你可以随后启动部署资源提供程序、 创建[提供](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)，并填充 Azure 堆栈[marketplace](azure-stack-marketplace.md)。
