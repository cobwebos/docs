---
title: 配置混合云标识与 Azure 和 Azure 堆栈的应用程序 |Microsoft 文档
description: 了解如何配置与 Azure 和 Azure 堆栈的应用程序的混合云标识。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 9a025716c2bb6266c1c1c552a2d0791b39429cac
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>教程： 配置混合云标识与 Azure 和 Azure 堆栈的应用程序

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

具有两个选项以授予对你在全局 Azure 和 Azure 堆栈中的应用程序访问权限。 当 Azure 堆栈具有持续连接到 Internet 时，你可以使用 Azure Active Directory (Azure AD)。 当 Internet 连接断开时 Azure 堆栈时，你可以使用 Azure Directory Federated Services (AD FS)。 使用服务主体授予对您为了部署或通过 Azure 堆栈中的 Azure 资源管理器配置 Azure 堆栈的应用程序访问权限。 

在本教程中，你将生成到示例环境：

> [!div class="checklist"]
> * 建立全局 Azure 和 Azure 堆栈中的混合标识
> * 检索用于访问 Azure 堆栈 API 的令牌。

执行这些步骤需要你具有 Azure 堆栈的操作员权限。

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在门户中的 Azure ad 中创建服务主体

如果已使用 Azure AD 部署 Azure Stack 作为标识存储，则可以创建服务主体，就像对 Azure 所做的那样。 [本文档](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad)演示如何执行通过门户的步骤。 在开始之前，请检查是否具有[所需的 Azure AD 权限](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>创建服务主体使用 PowerShell 的 AD fs

如果已使用 AD FS 部署 Azure Stack，则可以使用 PowerShell 创建服务主体、为角色分配访问权限以及使用该标识从 PowerShell 登录。 [本文档](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs)演示如何使用 PowerShell 执行步骤。

## <a name="using-the-azure-stack-api"></a>使用 Azure 堆栈 API

[本教程](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use)将引导你完成中检索令牌以访问 Azure 堆栈 API 的过程。

## <a name="connect-to-azure-stack-using-powershell"></a>连接到 Azure 堆栈使用 Powershell

下面是使用讲授了用于连接到 Azure 堆栈本文档中的概念的示例脚本。

### <a name="prerequisites"></a>必备组件

Azure 堆栈安装连接到 Azure Active Directory 与可以访问的订阅。 如果你没有 Azure 堆栈安装，你可以使用这些说明设置[Azure 堆栈开发工具包](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy)。

[本教程](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart)将引导你完成安装 Azure PowerShell 并连接到你的 Azure 堆栈安装所需的步骤。

#### <a name="connect-to-azure-stack-using-code"></a>连接到 Azure 堆栈使用代码

若要连接到 Azure 堆栈使用代码，使用 Azure 资源管理器终结点 API 的身份验证和 graph 终结点获取你 Azure 堆栈的安装，并使用 REST 请求然后进行身份验证。 你可以找到示例应用程序[此处](https://github.com/shriramnat/HybridARMApplication)。

> [!note]  
Azure SDK for 所用语言的支持的 Azure API 配置文件，除非 SDK 可能无法用于 Azure 堆栈。 若要了解有关 Azure API 配置文件的详细信息，请转[此处](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles)。

## <a name="next-steps"></a>后续步骤

 - 若要了解有关如何在 Azure 堆栈中处理标识的详细信息，请参阅[标识 Azure 堆栈的体系结构](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture)。  
 - 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
