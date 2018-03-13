---
title: "在 Visual Studio 中使用连接服务添加 Azure Active Directory | Microsoft Docs"
description: "使用 Visual Studio 中的“添加连接服务”对话框添加 Azure Active Directory"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>在 Visual Studio 中使用连接服务添加 Azure Active Directory
通过使用 Azure Active Directory (Azure AD)，可以支持 ASP.NET MVC Web 应用程序或 Web API 服务中的 Active Directory 身份验证的单一登录 (SSO)。 通过 Azure Active Directory 身份验证，用户可以使用其帐户从 Azure Active Directory 连接到 Web 应用程序。 通过 Web API 的 Azure Active Directory 身份验证的优点包括，从 Web 应用程序公开 API 时提供增强的数据安全性。 通过 Azure AD，不需要使用其自己的帐户和用户管理来管理单独的身份验证系统。

## <a name="prerequisites"></a>先决条件
- Azure 帐户 - 如果没有 Azure 帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>使用“连接服务”对话框连接到 Azure Active Directory
1. 在 Visual Studio 中，创建或打开 ASP.NET MVC 项目或 ASP.NET Web API 项目。

1. 在“解决方案资源管理器”中，右键单击“连接服务”节点，并在上下文菜单中选择“添加连接的服务”。

1. 在“连接服务”页，选择“使用 Azure Active Directory 进行身份验证”。
   
    ![“连接服务”页](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. 在“配置 Azure AD 身份验证”向导的“简介”页中，选择“下一步”。
   
    ![“简介”页](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. 在“配置 Azure AD 身份验证”向导的“单一登录”页中，从“域”下拉列表中选择域。 域列表包含在“帐户设置”对话框中列出的帐户可以访问的所有域。 如果没有找到要查找的域，作为替代方法，可以输入域名，如 `mydomain.onmicrosoft.com`。 可以选择用于创建 Azure Active Directory 应用的选项，也可以使用现有 Azure Active Directory 应用中的设置。 完成后，选择“下一步”。
   
    ![“单一登录”页](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. 在“配置 Azure AD 身份验证”向导的“目录访问权限”页中，请确保已选中“读取目录数据”选项。 
   
    ![“目录访问权限”页](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. 选择“完成”以添加必要的配置代码和引用，以便为 Azure AD 身份验证启用项目。 可以在 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)中看到 Active Directory 域。

1. Visual Studio 会显示[发生了什么情况](#how-your-project-is-modified)一文，显示项目的修改情况。 如果想要检查一切是否正常运作，请打开已修改的配置文件之一并验证其中是否存在本文中提及的设置。 

## <a name="how-your-project-is-modified"></a>项目的修改情况
当运行向导时，Visual Studio 会将 Azure Active Directory 和关联的引用添加到项目。 还会修改项目中的配置文件和代码文件以添加对 Azure AD 的支持。 Visual Studio 所做的特定修改取决于项目类型。 有关 ASP.NET MVC 项目修改情况的详细信息，请参阅[发生了什么 – MVC 项目](http://go.microsoft.com/fwlink/p/?LinkID=513809)。 对于 Web API 项目，请参阅[发生了什么 – Web API 项目](http://go.microsoft.com/fwlink/p/?LinkId=513810)。

## <a name="next-steps"></a>后续步骤
* [Azure Active Directory 的 MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Azure Active Directory 文档](https://azure.microsoft.com/documentation/services/active-directory/)
* [博客文章：Azure Active Directory 简介](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

