---
title: 安装 Visual Studio 并连接到 Azure Stack | Microsoft Docs
description: 了解安装 Visual Studio 并连接到 Azure Stack 所需的步骤
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>安装 Visual Studio 并连接到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Visual Studio 在 Azure Stack 中创建和部署 Azure 资源管理器[模板](azure-stack-arm-templates.md)。 可以按照本文中所述的步骤，从 [Azure Stack 开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或从基于 Windows 的外部客户端（如果已通过 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 连接）安装 Visual Studio。 本文中的这些步骤适用于 Visual Studio 2015 Community Edition 的全新安装。 阅读更多有关[共存](https://msdn.microsoft.com/library/ms246609.aspx)与其他 Visual Studio 版本。

## <a name="install-visual-studio"></a>安装 Visual Studio

1. 下载并运行此 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。
2. 搜索**与 Microsoft Azure SDK-2.9.6 的 Visual Studio Community 2015**，选择**添加**，然后选择**安装**。

    ![屏幕捕获的 WebPI 安装步骤](./media/azure-stack-install-visual-studio/image1.png)

3. 卸载**Microsoft Azure PowerShell**作为 Azure SDK 的一部分安装。

    ![有关 Azure PowerShell 的添加/删除程序接口的屏幕捕获](./media/azure-stack-install-visual-studio/image2.png)

4. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)

5. 安装完成后，重启操作系统。

## <a name="connect-to-azure-stack"></a>连接到 Azure Stack

1. 启动 Visual Studio。

2. 在“视图”菜单中，选择“云资源管理器”。

3. 在新窗格中，选择“添加帐户”并使用 Azure Active Directory 凭据登录。
    ![云资源管理器的登录并连接到 Azure 堆栈后的屏幕捕获](./media/azure-stack-install-visual-studio/image6.png)

登录后，可以[部署模板](azure-stack-deploy-template-visual-studio.md)或浏览可用的资源类型和资源组以创建自己的模板。

## <a name="next-steps"></a>后续步骤

* [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
