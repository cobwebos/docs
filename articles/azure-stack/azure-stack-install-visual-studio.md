---
title: "安装 Visual Studio 并连接到 Azure 堆栈 |Microsoft 文档"
description: "了解安装 Visual Studio 并连接到 Azure 堆栈所需的步骤"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: e73aba26a2409c710d962b9df45f6601b0e05340
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>安装 Visual Studio 并连接到 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用 Visual Studio 来创作和部署 Azure 资源管理器[模板](user/azure-stack-arm-templates.md)Azure 堆栈中。 你可以使用本文中所述的步骤安装 Visual Studio 从[Azure 堆栈开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果通过连接[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。 这些步骤执行 Visual Studio 2015 Community Edition 的全新安装。 阅读更多有关[共存](https://msdn.microsoft.com/library/ms246609.aspx)其他 Visual Studio 版本之间。

## <a name="install-visual-studio"></a>安装 Visual Studio
1. 下载并运行[Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。             
2. 搜索**与 Microsoft Azure SDK-2.9.6 的 Visual Studio Community 2015**，单击**添加**，和**安装**。

    ![屏幕快照的 WebPI 安装步骤](./media/azure-stack-install-visual-studio/image1.png) 

3. 卸载**Microsoft Azure PowerShell**作为 Azure SDK 的一部分安装。

    ![有关 Azure PowerShell 的添加/删除程序接口的屏幕截图](./media/azure-stack-install-visual-studio/image2.png) 

4. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)

5. 在安装完成后，重新启动操作系统。

## <a name="connect-to-azure-stack"></a>连接到 Azure Stack

1. 启动 Visual Studio。

2. 在“视图”菜单中，选择“云资源管理器”。

3. 在新窗格中，选择**添加帐户**并使用 Azure Active Directory 凭据登录。  
    ![登录并连接到 Azure 堆栈后的屏幕截图的云资源管理器](./media/azure-stack-install-visual-studio/image6.png)

登录后，你可以[将模板部署](user/azure-stack-deploy-template-visual-studio.md)或浏览可用的资源类型和资源组以创建你自己的模板。  

## <a name="next-steps"></a>后续步骤

 - [为 Azure Stack 开发模板](user/azure-stack-develop-templates.md)
