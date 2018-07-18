---
title: 安装 Visual Studio 并连接到 Azure Stack | Microsoft Docs
description: 了解安装 Visual Studio 并连接到 Azure Stack 所需的步骤
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295024"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>安装 Visual Studio 并连接到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以使用 Visual Studio 编写和部署 Azure 资源管理器[模板](azure-stack-arm-templates.md)到 Azure 堆栈。 这篇文章中的步骤将引导你完成在上安装 Visual Studio [Azure 堆栈](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或如果你要通过 Azure 堆栈外部计算机上[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

## <a name="install-visual-studio"></a>安装 Visual Studio

1. 下载并运行此 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。  

2. 打开**Microsoft Web 平台安装程序**。

3. 搜索**Visual Studio Community 2015 with Microsoft Azure SDK-2.9.6**。 单击**添加**，和**安装**。

4. 卸载**Microsoft Azure PowerShell**作为 Azure SDK 的一部分安装。

    ![WebPI 安装步骤的屏幕截图](./media/azure-stack-install-visual-studio/image1.png) 

5. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)

6. 安装完成后，重启操作系统。

## <a name="connect-to-azure-stack-with-azure-ad"></a>连接到与 Azure AD 的 Azure 堆栈

1. 启动 Visual Studio。

2. 在“视图”菜单中，选择“云资源管理器”。

3. 在新窗格中，选择**添加帐户**并使用 Azure Active Directory (Azure AD) 凭据登录。  

    ![登录并连接到 Azure 堆栈后的屏幕截图的云资源管理器](./media/azure-stack-install-visual-studio/image2.png)

登录后，可以[部署模板](azure-stack-deploy-template-visual-studio.md)或浏览可用的资源类型和资源组以创建自己的模板。  

## <a name="connect-to-azure-stack-with-ad-fs"></a>连接到使用 AD FS 的 Azure 堆栈

1. 启动 Visual Studio。

2. 从**工具**，选择**选项**。

3. 展开**环境**中**导航窗格中**和选择**帐户**。

4. 选择**添加**，然后输入用户 Azure 资源管理器终结点。  
  对于 Azure 堆栈开发工具包中，URL 是： `https://management.local.azurestack/external`。  
  用于 Azure 堆栈集成系统 URL 是： `https://management.[Region}.[External FQDN]`。

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. 选择 **添加** 。  

    Visual Studio 调用 Azure 资源管理器，并发现 Azure Directory 联合服务 (AD FS) 包括身份验证终结点的终结点。

    ![登录并连接到 Azure 堆栈后的屏幕截图的云资源管理器](./media/azure-stack-install-visual-studio/image6.png)

6. 选择**云资源管理器**从**视图**菜单。
7. 选择**添加帐户**并使用 AD FS 凭据登录。  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    云资源管理器查询可用的订阅。 你可以选择一个可用的订阅来管理。

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. 浏览你的现有资源、 资源组，或将模板部署。

## <a name="next-steps"></a>后续步骤

 - 详细了解与其他 Visual Studio 版本之间的[共存](https://msdn.microsoft.com/library/ms246609.aspx)。
 - [为 Azure Stack 开发模板](azure-stack-develop-templates.md)