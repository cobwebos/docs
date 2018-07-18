---
title: 使用证书通过 Azure AD 报告 API 获取数据 | Microsoft Docs
description: 介绍如何在没有用户干预的情况下，使用证书凭据通过 Azure AD 报告 API 从目录获取数据。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: aa0891126ad6fa05a39b9245e4fe85b61218ec40
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222454"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>使用证书通过 Azure Active Directory 报告 API 获取数据

[Azure Active Directory (Azure AD) 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。

如果想要访问 Azure AD 报告 API 而无需用户干预，则可以配置为使用证书进行访问

本文：

- 为你提供使用证书访问 Azure AD 报告 API 所需的步骤。
- 假定你已完成[访问 Azure Active Directory 报告 API 的先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)。 


若要使用证书访问报告 API，需要：

1. 安装必备组件
2. 在应用中设置证书 
3. 授予权限
4. 获取访问令牌




有关源代码的信息，请参阅 [Leverage Report API Module](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils)（利用报告 API 模块）。 

## <a name="install-prerequisites"></a>安装必备组件

需要已安装 Azure AD PowerShell V2 和 AzureADUtils 模块。

1. 按照 [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) 中的说明，下载并安装 Azure AD Powershell V2。

2. 从 [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1) 下载 Azure AD Utils 模块。 
  此模块提供多个实用程序 cmdlet，包括：
    - 使用 NuGet 的最新版本的 ADAL
    - 使用 ADAL 的用户、应用程序密钥和证书中的访问令牌
    - 处理分页结果的图形 API

**安装 Azure AD Utils 模块：**

1. 创建用于保存实用程序模块的目录（例如 C:\azureAD）并从 GitHub 下载该模块。
2. 打开 PowerShell 会话，转到刚创建的目录。 
3. 导入该模块，使用 Install-AzureADUtilsModule cmdlet 将其安装在 PowerShell 模块路径中。 

会话应如以下屏幕所示：

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>在应用中设置证书

**若要在应用中设置证书，请执行以下操作：**

1. 从 Azure 门户[获取应用的对象 ID](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id)。 

  ![Azure 门户](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. 打开 PowerShell 会话，使用 Connect-AzureAD cmdlet 连接到 Azure AD。

  ![Azure 门户](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. 使用 AzureADUtils 中的 New-AzureADApplicationCertificateCredential cmdlet 向其添加证书凭据。 

>[!Note]
>需提供此前捕获的应用程序对象 ID，以及证书对象（使用 Cert: 驱动器获取）。
>


  ![Azure 门户](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>获取访问令牌

若要获取访问令牌，请使用 AzureADUtils 中的 **Get-AzureADGraphAPIAccessTokenFromCert** cmdlet。 

>[!NOTE]
>需使用应用程序 ID，而不是在上一节使用的对象 ID。
>

 ![Azure 门户](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>使用访问令牌调用图形 API

现在可以创建脚本。 下面是一个使用 AzureADUtils 中的 **Invoke-AzureADGraphAPIQuery** cmdlet 的示例。 该 cmdlet 处理多分页结果，然后将这些结果发送到 PowerShell 管道。 

 ![Azure 门户](./media/active-directory-report-api-with-certificates/script-completed.png)

现在可以导出到 CSV 并保存到 SIEM 系统。 也可以将脚本包装到计划的任务中，以便从租户定期获取 Azure AD 数据，不需将应用程序密钥存储在源代码中。 

## <a name="next-steps"></a>后续步骤

- [获取报告 API 的第一印象](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [创建自己的解决方案](active-directory-reporting-api-getting-started-azure-portal.md#customize)




