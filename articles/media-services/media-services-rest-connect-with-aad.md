---
title: "通过 Azure AD 身份验证使用 REST 访问 Azure 媒体服务 API | Microsoft Docs"
description: "了解如何通过 Azure Active Directory 身份验证使用 REST 访问 Azure 媒体服务 API。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>通过 Azure AD 身份验证使用 REST 访问 Azure 媒体服务 API

通过 Azure 媒体服务使用 Azure AD 身份验证时，可以通过以下两种方式之一进行身份验证：

- **用户身份验证**：对使用应用程序与 Azure 媒体服务资源进行交互的人员执行身份验证。 交互式应用程序应首先提示用户输入凭据。 举个例子，授权用户用来监视编码作业或实时流式处理的管理控制台应用程序。 
- **服务主体身份验证**：对服务进行身份验证。 通常使用此身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用：如 Web 应用、函数应用、逻辑应用、 API 或微服务。

    本教程演示如何通过 Azure AD“服务主体”身份验证使用 REST 访问 AMS API。 

    > [!NOTE]
    > “服务主体”是为连接到 Azure 媒体服务的大多数应用程序推荐的最佳做法。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 从 Azure 门户获取身份验证信息
> * 使用 Postman 获取访问令牌
> * 使用访问令牌测试资产 API


> [!IMPORTANT]
> 目前，媒体服务支持 Azure 访问控制服务身份验证模型。 不过，访问控制身份验证将于 2018 年 6 月 1 日弃用。 建议尽快迁移到 Azure AD 身份验证模型。

## <a name="prerequisites"></a>先决条件

- 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [使用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)
- 查看[通过 ADD 身份验证访问 Azure 媒体服务 API 概述](media-services-use-aad-auth-to-access-ams-api.md)一文。
- 安装 [Postman](https://www.getpostman.com/) REST 客户端以执行本文所示的 REST API。 

    本教程中要使用的是 Postman，但任何 REST 工具也适用。 其他适用的工具有：具有 REST 插件的 Visual Studio Code 或 Telerik Fiddler。 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>从 Azure 门户获取身份验证信息

### <a name="overview"></a>概述

若要访问媒体服务 API，需要收集以下数据点。

|设置|示例|说明|
|---|-------|-----|
|Azure Active Directory 租户域|microsoft.onmicrosoft.com|使用以下格式创建作为安全令牌服务 (STS) 终结点的 Azure AD：https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token。 Azure AD 颁发用于访问资源的 JWT（访问令牌）。|
|REST API 终结点|https://amshelloworld.restv2.westus.media.azure.net/api/|这是应用程序中发出的所有媒体服务 REST API 调用所针对的终结点。|
|客户端 ID（应用程序 ID）|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD 应用程序（客户端）ID。 需要客户端 ID 才能获取访问令牌。 |
|客户端机密|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD 应用程序密钥（客户端密码）。 需要客户端密码才能获取访问令牌。|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>从 Azure 门户获取 AAD 身份验证信息

若要获取信息，请按照以下步骤操作：

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 导航到 AMS 实例。
3. 选择“API 访问”。
4. 点击“通过服务主体连接到 Azure 媒体服务 API”。

    ![API 访问](./media/connect-with-rest/connect-with-rest01.png)

5. 选择现有的“Azure AD 应用程序”或新建一个 Azure AD 应用程序（如下所示）。

    > [!NOTE]
    > 为使 Azure 媒体 REST 请求成功，对于尝试访问的媒体服务帐户，调用用户必须具有“参与者”或“所有者”角色。 如果出现“远程服务器返回错误: (401)未授权”的异常，请参阅[访问控制](media-services-use-aad-auth-to-access-ams-api.md#access-control)。

    如果需要创建新的 AD 应用，请执行以下步骤：
    
    1. 按“新建”。
    2. 输入名称。
    3. 再次按“新建”。
    4. 按“保存”。

    ![API 访问](./media/connect-with-rest/new-app.png)

    新的应用将显示在页面上。

6. 获取“客户端 ID”（应用程序 ID）。
    
    1. 选择应用程序。
    2. 从右侧的窗口获取“客户端 ID”。 

    ![API 访问](./media/connect-with-rest/existing-client-id.png)。

7.  获取应用程序的“密钥”（客户端密码）。 

    1. 单击“管理应用程序”按钮（请注意，客户端 ID 信息位于“应用程序 ID”的下方）。 
    2. 按“密钥”。
    
        ![API 访问](./media/connect-with-rest/manage-app.png)
    3. 填写“说明”和“到期时间”，然后按“保存”以生成应用密钥（客户端密码）。
    
        按下“保存”按钮后将显示密钥值。 在退出此边栏选项卡之前复制此密钥值。

    ![API 访问](./media/connect-with-rest/connect-with-rest03.png)

可以将 AD 连接参数的值添加到 web.config 或 app.config 文件，稍后在代码中使用。

> [!IMPORTANT]
> “客户端密钥”是重要的密码，应该正确地在密钥保管库中受到保护或在生产中加密。

## <a name="get-the-access-token-using-postman"></a>使用 Postman 获取访问令牌

本部分演示如何使用 Postman 执行返回 JWT 持有者令牌（访问令牌）的 REST API。 若要调用任何媒体服务 REST API，需要将“授权”标头添加到这些调用，并将“持有者 your_access_token”的值添加到每个调用（如本教程的下一部分中所示）。 

1. 打开 Postman。
2. 选择“POST” 。
3. 使用以下格式输入包括租户名称的 URL：此租户名称应以“.onmicrosoft.com”结尾，此 URL 应以“oauth2/token”结尾： 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. 选择“标头”选项卡。
5. 使用“密钥/值”数据网格输入“标头”信息。 

    ![数据网格](./media/connect-with-rest/headers-data-grid.png)

    或者，单击 Postman 窗口右侧的“批量编辑”链接，然后粘贴以下代码。

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. 按“正文”选项卡。
7. 使用“密钥/值”数据网格输入正文信息（替换客户端 ID 和密钥值）。 

    ![数据网格](./media/connect-with-rest/data-grid.png)

    或者，单击 Postman 窗口右侧的“批量编辑”，然后粘贴以下正文（替换客户端 ID 和密钥值）：

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. 按“发送”。

    ![获取令牌](./media/connect-with-rest/connect-with-rest04.png)

返回的响应包含需要用于访问任何 AMS API 的访问令牌。

## <a name="test-the-assets-api-using-the-access-token"></a>使用访问令牌测试资产 API

本部分演示如何使用 Postman 访问资产 API。

1. 打开 Postman。
2. 选择“GET” 。
3. 粘贴 REST API 终结点（例如 https://amshelloworld.restv2.westus.media.azure.net/api/Assets）
4. 选择“授权”选项卡。 
5. 选择“持有者令牌”。
6. 粘贴上一部分中创建的令牌。

    ![获取令牌](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX 在 Mac 和电脑上可能有所不同。 如果 Mac 版本的“身份验证”部分下拉列表中没有“持有者令牌”选项，应在 Mac 客户端上手动添加“授权”标头。

   ![“授权”标头](./media/connect-with-rest/auth-header.png)

7. 选择“标头”。
5. 单击 Postman 窗口右侧的“批量编辑”链接。
6. 粘贴以下标头：

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. 按“发送”。

返回的响应包含帐户中的资产。

## <a name="next-steps"></a>后续步骤

* 尝试使用 [Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln)（Azure 媒体服务访问的 Azure AD 身份验证：均通过 REST API）中的此示例代码
* [使用 .NET 上传文件](media-services-dotnet-upload-files.md)
