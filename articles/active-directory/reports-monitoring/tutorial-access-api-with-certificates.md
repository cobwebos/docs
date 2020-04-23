---
title: 带证书的 AD 报告 API 教程 |微软文档
description: 本教程介绍如何在没有用户干预的情况下，使用证书凭据通过 Azure AD 报告 API 从目录获取数据。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2808c8431a6b98b162920fb58a6e2ac0498d2055
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081704"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>教程：使用证书通过 Azure Active Directory 报告 API 获取数据

[Azure Active Directory (Azure AD) 报告 API](concept-reporting-api.md) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。 如果想要访问 Azure AD 报告 API 而无需用户干预，则必须配置对证书的访问权限。

本教程介绍如何使用测试证书访问 MS 图形 API 以进行报告。 建议不要在生产环境中使用测试证书。 

## <a name="prerequisites"></a>先决条件

1. 若要访问登录数据，请确保拥有一个使用高级 (P1/P2) 许可证的 Azure Active Directory 租户。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 请注意，如果在升级之前没有任何活动数据，则在升级到高级版许可证后，数据需要经过几天才会显示在报表中。 

2. 创建或切换到属于该租户的全局管理员、安全管理员、安全读取者或报表读取者角色的用户帐户****************。 

3. 完成[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)。 

4. 下载并安装 [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)。

5. 安装 [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/)。 此模块提供多个实用程序 cmdlet，包括：
    - 身份验证所需的 ADAL 库
    - 使用 ADAL 的用户、应用程序密钥和证书中的访问令牌
    - 处理分页结果的图形 API

6. 如果这是你第一次使用模块运行**安装-MSCloudIdUtils模块**，否则使用**导入模块**PowerShell 命令导入它。 您的会话应类似于此屏幕：Windows ![PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. 使用 **"新建自签名证书**PowerShell 命令"创建测试证书。

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. 使用 Export-Certificate commandlet 将其导出到证书文件****。

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>使用证书通过 Azure Active Directory 报告 API 获取数据

1. 导航到 [Azure 门户](https://portal.azure.com)，选择“Azure Active Directory”，然后选择“应用注册”并从列表中选择应用程序********。 

2. 在"应用程序注册"边栏选项卡的 **"管理**"部分下选择**证书&机密**，然后选择 **"上传证书**"。

3. 从上一步中选择证书文件，然后选择 **"添加**"。 

4. 请注意应用程序 ID 以及刚刚使用应用程序注册的证书的指纹。 要查找指纹，请转到 **"管理**"部分下的 **"证书&机密**"。 指纹将"**证书"** 列表下。

5. 在内联清单编辑器中打开应用程序清单，并验证*密钥凭据*属性是否更新了您的新证书信息，如下所示 ： 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. 现在，可以使用此证书获取 MS 图形 API 的访问令牌。 使用 MSCloudIdUtils PowerShell 模块中的 Get-MSCloudIdMSGraphAccessTokenFromCert cmdlet，传入从上一步获取的应用程序 ID 和指纹****。 

   ![Azure 门户](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. 使用 PowerShell 脚本中的访问令牌查询图形 API。 使用 MSCloudIDUtils 中的 Invoke-MSCloudIdMSGraphQuery cmdlet 来枚举 signins 和 directoryAudits 终结点****。 该 cmdlet 处理分多页的结果，并将这些结果发送到 PowerShell 管道。

8. 查询 directoryAudits 终结点以检索审核日志。 
   ![Azure 门户](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. 查询 signins 终结点以检索登录日志。
    ![Azure 门户](./media/tutorial-access-api-with-certificates/query-signins.png)

10. 现在可以选择将此数据导出为 CSV 并保存到 SIEM 系统。 也可以将脚本包装到计划的任务中，以便从租户定期获取 Azure AD 数据，不需将应用程序密钥存储在源代码中。 

## <a name="next-steps"></a>后续步骤

* [获取报告 API 的第一印象](concept-reporting-api.md)
* [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
