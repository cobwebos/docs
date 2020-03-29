---
title: 共享点文件 - QnA 制造商
description: 将安全的 SharePoint 数据源添加到您的知识库，以丰富知识库，其中可以用 Active Directory 保护的问题和答案。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294875"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>将安全的 SharePoint 数据源添加到知识库

将安全的基于云的 SharePoint 数据源添加到您的知识库，以丰富知识库，提供可能通过 Active Directory 保护的问题和答案。

当您将安全的 SharePoint 文档添加到知识库时，作为 QnA 制造商管理器，您必须请求 QnA Maker 的活动目录权限。 一旦从 Active Directory 管理器授予 QnA Maker 以访问 SharePoint 的权限，就不必再次授予它。 如果每个后续文档添加到知识库将不需要授权，如果它位于同一 SharePoint 资源中。

如果 QnA Maker 知识库管理器不是活动目录管理器，则需要与活动目录管理器通信才能完成此过程。

## <a name="prerequisites"></a>先决条件

* 基于云的 SharePoint - QnA 制造商使用 Microsoft 图形获得权限。 如果您的 SharePoint 是本地的，您将无法从 SharePoint 中提取，因为 Microsoft 图形无法确定权限。
* URL 格式 - QnA Maker 仅支持为共享而生成的 SharePoint URL，该 URL 具有格式`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>将受支持的文件类型添加到知识库

您可以将所有 QnA Maker 支持[的文件类型](../Concepts/content-types.md)从 SharePoint 网站添加到您的知识库。 如果文件资源是安全的，您可能需要授予[权限](#permissions)。

1. 从具有 SharePoint 站点的库中，选择文件的省略号菜单`...`。
1. 复制文件的 URL。

   ![通过选择文件的省略号菜单，然后复制 URL，获取 SharePoint 文件 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. 在"设置 **"** 页上的 QnA Maker 门户中，[将 URL 添加到](manage-knowledge-bases.md#edit-knowledge-base)知识库。

### <a name="images-with-sharepoint-files"></a>具有 SharePoint 文件的映像

如果文件包含图像，则不会提取图像。 在将文件提取到 QnA 对后，可以从 QnA Maker 门户添加图像。

使用以下标记语法添加图像：

```markdown
![Explanation or description of image](URL of public image)
```

方括号中的文本`[]`，解释图像。 括号`()`中的 URL 是指向图像的直接链接。

在交互式测试面板（在 QnA Maker 门户中）中测试 QnA 对时，将显示图像，而不是标记文本。 这将验证可以从客户端应用程序公开检索映像。

## <a name="permissions"></a>权限

当从 SharePoint 服务器添加安全文件到知识库时，将授予权限。 根据 SharePoint 的设置方式和添加文件的人员的权限，这可能需要：

* 没有其他步骤 - 添加文件的人具有所需的所有权限。
* [知识库管理器](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)和[活动目录管理器](#active-directory-manager-grant-file-read-access-to-qna-maker)的步骤。

请参阅下面列出的步骤。

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知识库管理器：在 QnA Maker 门户中添加 SharePoint 数据源

当**QnA Maker 管理器**将安全的 SharePoint 文档添加到知识库时，知识库管理器将发起活动目录管理器需要完成的权限请求。

请求以弹出窗口开头，以对活动目录帐户进行身份验证。

![验证用户帐户](../media/add-sharepoint-datasources/authenticate-user-account.png)

一旦 QnA Maker 管理器选择帐户，Azure 活动目录管理员将收到一条通知，指出他们需要允许 QnA Maker 应用（而不是 QnA Maker 管理器）访问 SharePoint 资源。 Azure 活动目录管理器需要为每个 SharePoint 资源执行此操作，但不是该资源中的每个文档。

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>活动目录管理器：授予对 QnA 制造商的文件读取访问权限

活动目录管理器（不是 QnA Maker 管理器）需要通过选择[此链接](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)授权 QnA Maker 门户 SharePoint 企业应用具有文件读取权限，授予访问 QnA Maker 以访问 SharePoint 资源的访问权限。

![Azure 活动目录管理器以交互方式授予权限](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>从 Azure 活动目录管理中心授予访问权限

1. 活动目录管理器登录到 Azure 门户并打开**[企业应用程序](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**。

1. 搜索`QnAMakerPortalSharePoint`选择 QnA 制造商应用。

    [![在企业应用列表中搜索 QnAMakerPortalSharePoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 在 **"安全"下**，转到**权限**。 选择 **"授予组织管理员同意**"。

    [![为活动目录管理员选择经过身份验证的用户](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 选择具有授予活动目录权限的登录帐户。



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

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

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库展开协作](collaborate-knowledge-base.md)
