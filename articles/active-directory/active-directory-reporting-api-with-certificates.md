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
ms.openlocfilehash: 0da0e5d4b7dd8ff000d6c56716bea1b36935af01
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928900"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>使用证书通过 Azure Active Directory 报告 API 获取数据

[Azure Active Directory (Azure AD) 报告 API](active-directory-reporting-api-getting-started-azure-portal.md) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。 如果想要访问 Azure AD 报告 API 而无需用户干预，则可以配置为使用证书进行访问。

这包括以下步骤：

1. [安装必备组件](#install-prerequisites)
2. [在应用中注册证书](#register-the-certificate-in-your-app)
3. [获取 MS 图形 API 的访问令牌](#get-an-access-token-for-ms-graph-api)
4. [查询 MS 图形 API 终结点](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>安装必备组件

1. 首先，确保已完成[访问 Azure Active Directory 报告 API 的先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)。 

2. 按照 [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) 中的说明，下载并安装 Azure AD Powershell V2

3. 从 [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/) 安装 MSCloudIDUtils。 此模块提供多个实用程序 cmdlet，包括：
    - 身份验证所需的 ADAL 库
    - 使用 ADAL 的用户、应用程序密钥和证书中的访问令牌
    - 处理分页结果的图形 API

4. 如果是首次使用模块，请运行 Install-MSCloudIdUtilsModule 来完成设置，否则只需使用 Import-Module Powershell 命令来导入。

会话应如以下屏幕所示：

  ![Windows Powershell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>在应用中注册证书

1. 首先，转到“应用程序注册”页。 为此，请导航到 [Azure 门户](https://portal.azure.com)，单击“Azure Active Directory”，然后单击“应用注册”并从列表中选择应用程序。 

2. 然后，按照[使用 Azure AD 注册证书](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad)中的步骤注册应用程序。 

3. 请注意应用程序 ID 以及刚刚使用应用程序注册的证书的指纹。 若要查找指纹，在门户中的“应用程序”页，转到“设置”，然后单击“密钥”。 指纹将位于“公钥”列表下。

  
## <a name="get-an-access-token-for-ms-graph-api"></a>获取 MS 图形 API 的访问令牌

若要获取 MS 图形 API 的访问令牌，请使用 MSCloudIdUtils PowerShell 模块中的 Get MSCloudIdMSGraphAccessTokenFromCert cmdlet。 

>[!NOTE]
>需要使用应用程序 ID（也称为 ClientID）以及证书的证书指纹，该证书的私钥安装在计算机的证书存储（CurrentUser 或 LocalMachine 证书存储）中。
>

 ![Azure 门户](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>使用访问令牌调用图形 API

现在可在 Powershell 脚本中使用访问令牌来查询图形 API。 以下示例使用 MSCloudIDUtils 中的 Invoke-MSCloudIdMSGraphQuery cmdlet 来枚举 signins 或 diectoryAudits 终结点。 该 cmdlet 处理多分页结果，然后将这些结果发送到 PowerShell 管道。

### <a name="query-the-directoryaudits-endpoint"></a>查询 DirectoryAudits 终结点
 ![Azure 门户](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>查询 SignIns 终结点
 ![Azure 门户](./media/active-directory-reporting-api-with-certificates/query-signins.png)

现在可以选择将此数据导出为 CSV 并保存到 SIEM 系统。 也可以将脚本包装到计划的任务中，以便从租户定期获取 Azure AD 数据，不需将应用程序密钥存储在源代码中。 


## <a name="next-steps"></a>后续步骤

- [获取报告 API 的第一印象](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [创建自己的解决方案](active-directory-reporting-api-getting-started-azure-portal.md#customize)




