---
title: SharePoint 文件-QnA Maker
description: 通过将受保护的 SharePoint 数据源添加到知识库，可以通过 Active Directory 的问题和答案来丰富知识库。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 93f17e79834b412ce0babf220ba13649ae07718c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660309"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>向知识库添加受保护的 SharePoint 数据源

将基于云的安全 SharePoint 数据源添加到知识库，以利用可以 Active Directory 的问题和答案来丰富知识库。

在将受保护的 SharePoint 文档添加到知识库中时，必须为 QnA Maker 管理器请求 QnA Maker Active Directory 权限。 将此权限提供给 Active Directory 管理器以 QnA Maker 访问 SharePoint 后，就不必再次提供该权限。 添加到知识库的每个后续文档在同一 SharePoint 资源中时不需要授权。

如果 QnA Maker 知识库管理器不是 Active Directory 管理器，则需要与 Active Directory 管理器进行通信以完成此过程。

## <a name="prerequisites"></a>必备条件

* 基于云的 SharePoint-QnA Maker 使用 Microsoft Graph 权限。 如果你的 SharePoint 位于本地，你将无法从 SharePoint 中提取，因为 Microsoft Graph 无法确定权限。
* URL 格式-QnA Maker 仅支持为共享生成的 SharePoint url，格式为`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>将支持的文件类型添加到知识库

您可以将所有 QnA Maker 支持的[文件类型](../Concepts/content-types.md)从 SharePoint 站点添加到知识库中。 如果文件资源受到保护，则可能必须授予[权限](#permissions)。

1. 从具有 SharePoint 站点的库中，选择该文件的省略号菜单 `...` 。
1. 复制该文件的 URL。

   ![通过选择文件的省略号菜单并复制 URL 来获取 SharePoint 文件 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. 在 QnA Maker 门户中的 "**设置**" 页上，将 URL 添加到知识库中。

### <a name="images-with-sharepoint-files"></a>带有 SharePoint 文件的图像

如果文件包含图像，则不提取这些文件。 将文件提取到 QnA 对后，可以在 QnA Maker 门户中添加该图像。

用以下 markdown 语法添加映像：

```markdown
![Explanation or description of image](URL of public image)
```

方括号中的文本对图像进行 `[]` 了说明。 括号中的 URL `()` 是指向图像的直接链接。

在交互式测试面板的 "QnA Maker" 门户中测试 QnA 对时，会显示图像，而不是 markdown 文本。 这会验证是否可以从客户端应用程序公开检索映像。

## <a name="permissions"></a>权限

当 SharePoint 服务器中的安全文件添加到知识库中时，将会授予权限。 根据 SharePoint 的设置方式和添加该文件的人员的权限，这可能需要：

* 无其他步骤-添加文件的人员具有所需的所有权限。
* "[知识库管理器](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)" 和 " [Active Directory 管理器](#active-directory-manager-grant-file-read-access-to-qna-maker)" 中的步骤。

请参阅下面列出的步骤。

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知识库管理器：在 QnA Maker 门户中添加 SharePoint 数据源

当**QnA Maker 管理器**将受保护的 SharePoint 文档添加到知识库时，该知识库管理器将启动 Active Directory 管理器需要完成的权限请求。

请求以弹出窗口开头，以便向 Active Directory 帐户进行身份验证。

![验证用户帐户](../media/add-sharepoint-datasources/authenticate-user-account.png)

QnA Maker 管理器选择帐户后，Azure Active Directory 管理员将收到一条通知，指出他们需要允许 QnA Maker 的应用（而不是 QnA Maker manager）访问 SharePoint 资源。 对于每个 SharePoint 资源，Azure Active Directory 管理器都需要执行此操作，但不需要为该资源中的每个文档执行此操作。

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory 管理器：授予对 QnA Maker 的文件读取访问权限

Active Directory 管理器（而不是 QnA Maker 管理器）需要通过选择[此链接](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)来授权 QnA Maker 门户 SharePoint enterprise 应用具有文件读取权限，从而授予 QnA Maker 访问 SharePoint 资源的访问权限。

![Azure Active Directory 管理器以交互方式授予权限](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>授予 Azure Active Directory 管理中心的访问权限

1. Active Directory 管理器登录到 Azure 门户并打开**[企业应用程序](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**。

1. 搜索 `QnAMakerPortalSharePoint` 选择 QnA Maker 应用。

    [![搜索企业应用列表中的 QnAMakerPortalSharePoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 在 "**安全性**" 下，中转到 "**权限**"。 选择 "**授予组织的管理员许可**"。

    [![选择 Active Directory 管理员的经过身份验证的用户](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 选择有权授予 Active Directory 权限的登录帐户。



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
