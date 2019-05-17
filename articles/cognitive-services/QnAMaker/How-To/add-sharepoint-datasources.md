---
title: Sharepoint 文件-QnA Maker
titleSuffix: Azure Cognitive Services
description: 将受保护的 Sharepoint 数据源添加到您的知识库来丰富知识库的问题和答案可能会使用 Active Directory 保护的。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: d02c3fbb762e83584a9ea277ef8d5cc15b391f37
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792574"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>将受保护的 Sharepoint 数据源添加到您的知识库

将受保护的 Sharepoint 数据源添加到您的知识库来丰富知识库的问题和答案可能会使用 Active Directory 保护的。 

当将受保护的 Sharepoint 文档添加到知识库，作为 QnA Maker 管理器中，你必须请求 QnA Maker 的 Active Directory 权限。 一旦此权限授予从 Active Directory 管理器对 QnA Maker 以对 Sharepoint 的访问，无需再次提供。 每次后续文档添加到知识库是否处于相同的 Sharepoint 资源无需授权。 

如果 QnA Maker 知识库管理器不是 Active Directory 管理员，你将需要与要完成此过程的 Active Directory 管理器进行通信。

## <a name="add-supported-file-types-to-knowledge-base"></a>将支持的文件类型添加到知识库

您可以添加所有受支持 QnA Maker[文件类型](../Concepts/data-sources-supported.md)从 Sharepoint 服务器到您的知识库。 您可能要授予[权限](#permissions)如果受保护的文件资源。

1. 在 Sharepoint 服务器上，选择该文件的省略号菜单`...`。
1. 复制文件的 URL。

    ![选择该文件的省略号菜单，然后复制该 URL 获取 Sharepoint 文件 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker 门户中上**设置**页上，[将 URL 添加](edit-knowledge-base.md#add-datasource)到知识库。 

## <a name="permissions"></a>权限

授予的权限发生时从 Sharepoint 服务器的受保护的文件添加到知识库。 具体取决于如何设置 Sharepoint 上移和添加文件，这可能需要的人员的权限：

* 无需其他步骤-将文件添加的人员具有所需的所有权限。
* 通过这两步骤[知识库管理器](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)并[Active Directory 管理器](#active-directory-manager-grant-file-read-access-to-qna-maker)。

请参阅下面列出的步骤。 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知识库管理器： QnA Maker 门户中添加 Sharepoint 数据源

当**QnA Maker manager**将受保护的 Sharepoint 文档添加到知识库，知识库管理器启动 Active Directory 管理员必须为完成的权限的请求。

该请求开始使用弹出窗口向 Active Directory 帐户进行身份验证。 

![用户帐户进行身份验证](../media/add-sharepoint-datasources/authenticate-user-account.png)

一旦 QnA Maker 管理器中选择帐户，Active Directory 管理员将收到一条通知，他需要允许应用程序 （而不是 QnA Maker 经理） 访问 Sharepoint 资源的 QnA Maker。 Active Directory 管理器将需要为每个 Sharepoint 资源，但在该资源不是每个文档执行此操作。 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory 管理器： 文件读取访问权限授予 QnA Maker

Active Directory 管理员 （而不是 QnA Maker 经理） 必须为授予访问权限来访问 Sharepoint 资源选择的 QnA Maker[此链接](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)授权 QnA Maker 门户 Sharepoint 企业应用程序具有读取文件权限。 

![Azure Active Directory 管理器以交互方式授予的权限](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>从 Azure Active Directory 管理中心-授予访问权限

1. Active Directory 管理器登录到 Azure 门户并打开**[企业应用程序](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**。 

1. 搜索`QnAMakerPortalSharepoint`选择 QnA Maker 应用。 

    [![企业应用程序列表中搜索的 QnAMakerPortalSharepoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 下**安全**，请转到**权限**。 选择**授予为组织的管理员同意**。 

    [![选择身份验证的用户的 Active Directory 管理员](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 选择具有要授予权限的 Active Directory 权限的登录帐户。 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在您的知识库上进行协作](collaborate-knowledge-base.md)