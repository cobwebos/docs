---
title: 为 Azure 和 Azure Stack 应用程序配置混合云标识 | Microsoft Docs
description: 了解如何为 Azure 和 Azure Stack 应用程序配置混合云标识。
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
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808156"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>教程： 配置 Azure 和 Azure 堆栈的应用程序的混合云标识

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何配置 Azure 和 Azure 堆栈应用程序的混合云标识。

必须授予对你在全球 Azure 和 Azure 堆栈中的应用程序访问权限的两个选项。

 * 当 Azure 堆栈具有连续连接到 Internet 时，你可以使用 Azure Active Directory (Azure AD)。
 * 当 Azure Stack 从 Internet 断开了连接时，可以使用 Azure Directory 联合身份验证服务 (AD FS)。

使用服务主体授予对你 Azure 堆栈的应用程序来部署或 Azure 堆栈中使用 Azure 资源管理器配置访问权限。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> * 在全球 Azure 和 Azure Stack 中建立一个混合标识
> * 检索用于访问 Azure 堆栈 API 的令牌。

在本教程中，必须具有 Azure 堆栈操作员权限的步骤。

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在门户中创建适用于 Azure AD 的服务主体

如果已使用 Azure AD 部署 Azure Stack 作为标识存储，则可以创建服务主体，就像对 Azure 所做的那样。 [创建服务主体](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad)文向你介绍如何执行通过门户步骤。 检查以查看你已经有[所需的 Azure AD 权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)在开始之前。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>使用 PowerShell 创建适用于 AD FS 的服务主体

如果已使用 AD FS 部署 Azure Stack，则可以使用 PowerShell 创建服务主体、为角色分配访问权限以及使用该标识从 PowerShell 登录。 [为 AD FS 创建服务主体](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs)演示如何执行使用 PowerShell 所需的步骤。

## <a name="using-the-azure-stack-api"></a>使用 Azure Stack API

[Azure 堆栈 API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use)教程将指导你完成中检索令牌以访问 Azure 堆栈 API 的过程。

## <a name="connect-to-azure-stack-using-powershell"></a>使用 Powershell 连接到 Azure Stack

快速入门[，能获得启动并运行 Azure 堆栈中的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart)将指导你完成安装 Azure PowerShell 并连接到你的 Azure 堆栈安装所需的步骤。

### <a name="prerequisites"></a>必备组件

一个通过你可以访问的订阅连接到 Azure Active Directory 的 Azure Stack 安装。 如果没有 Azure Stack 安装，可以使用这些说明来安装 [Azure Stack 开发工具包](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)。

#### <a name="connect-to-azure-stack-using-code"></a>使用代码连接到 Azure Stack

若要使用代码连接到 Azure Stack，请使用 Azure 资源管理器终结点 API 来获取 Azure Stack 安装的身份验证和 Graph 终结点，然后使用 REST 请求进行身份验证。 您可以在找到示例客户端应用程序[GitHub](https://github.com/shriramnat/HybridARMApplication)。

>[!Note]
>除非所选语言的 Azure SDK 支持 Azure API 配置文件，否则 SDK 可能无法与 Azure Stack 一起使用。 若要了解有关 Azure API 配置文件的详细信息，请参阅[管理 API 版本配置文件](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles)文章。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 Azure Stack 如何处理标识，请参阅 [Azure Stack 的标识体系结构](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture)。
 - 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
