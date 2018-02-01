---
title: "Azure Active Directory 域服务：服务主体配置疑难解答 | Microsoft Docs"
description: "Azure AD 域服务的服务主体配置疑难解答"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Azure AD 域服务 - 服务主体配置疑难解答

Microsoft 使用各种[服务主体](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects)与目录进行通信，以便服务、管理和更新域。 如果某一服务主体配置错误或被删除，则可能在服务中导致中断。

## <a name="aadds102-service-principal-not-found"></a>AADDS102：找不到服务主体

警报消息：

已从 Azure AD 目录中删除 Azure AD 域服务正常工作所需的服务主体。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。

服务主体是 Microsoft 用于管理、更新和维护托管域的应用程序。 如果删除服务主体，将中断 Microsoft 为域提供服务的功能。 使用以下步骤确定需要重新创建的服务主体。

1. 导航到 Azure 门户中的[企业应用程序 - 所有应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)页。
2. 使用“显示”下拉列表，选择“所有应用程序”并单击“应用”。
3. 使用下表，通过将 ID 粘贴到搜索框中并按 Enter 来搜索每个应用程序 ID。 如果搜索结果为空，则必须按照“解决方法”列中的步骤重新创建服务主体。

| 应用程序 ID | 解决方法 |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [使用 PowerShell 重新创建缺失的服务主体](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [重新注册到 Microsoft.AAD 命名空间](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [重新注册到 Microsoft.AAD 命名空间](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [自动更正的服务主体](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>使用 PowerShell 重新创建缺失的服务主体

针对缺失的 ID 执行以下步骤：2565bd9d-da50-47d4-8b85-4c97f669dc36

**补救方法：**

需要 Azure AD PowerShell 来完成这些步骤。 有关安装 Azure AD PowerShell 的信息，请参阅[本文](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.)。

若要解决此问题，在 PowerShell 窗口中键入以下命令：
1. Install-Module AzureAD
2. Import-Module AzureAD
3. 通过执行以下 PowerShell 命令检查目录中是否缺失 Azure AD 域服务所需的服务主体：
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. 通过键入以下 PowerShell 命令创建服务主体：
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. 创建缺失的服务主体后，等待两个小时并检查域的运行状况。


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>使用 Azure 门户重新注册到 Microsoft.AAD 命名空间

针对缺失的 ID 执行以下步骤：443155a6-77f3-45e3-882b-22b3a8d431fb 和 abba844e-bc0e-44b0-947a-dc74e5d09022

**补救方法：**

使用以下步骤还原目录上的域服务：

1. 导航到 Azure 门户中的[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)页。
2. 从与托管域相关联的表中选择订阅
3. 使用左侧的导航，选择“资源提供程序”
4. 在表中搜索“Microsoft.AAD”并单击“重新注册”
5. 若要进行检查以确保警报已解决，在两个小时后查看运行状况警报页。


### <a name="service-principals-that-self-correct"></a>自动更正的服务主体

针对缺失的 ID 执行以下步骤：d87dcbc6-a371-462e-88e3-28ad15ec4e64

**补救方法：**

Microsoft 可确定服务主体的缺失、错误配置或删除。 为快速解决服务问题，Microsoft 将重新创建服务主体本身。 两个小时后检查域的运行状况，确保已重新创建主体。

## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
