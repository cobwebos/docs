---
title: 配置 Postman 以便进行 Azure 媒体服务 REST API 调用
description: 了解如何为媒体服务 REST API 调用配置 Postman。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: 24ee0083ec79265f0ac4dfc0a2e235bc72522831
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813885"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>配置 Postman 以便进行媒体服务 REST API 调用

本文演示如何配置 **Postman**，以便可以使用它调用 Azure 媒体服务 (AMS) REST API。 本文说明如何将环境和集合文件导入到 **Postman**。 集合包含调用 Azure 媒体服务 (AMS) REST API 的 HTTP 请求的分组定义。 环境文件包含集合使用的变量。

## <a name="prerequisites"></a>先决条件

- [创建媒体服务帐户](create-account-cli-how-to.md)。 请务必记住资源组名称和媒体服务帐户名称。 
- 获取[访问 API](access-api-cli-how-to.md) 所需的信息
- 安装 [Postman](https://www.getpostman.com/) REST 客户端，以便执行一些 AMS REST 教程中所示的 REST API。 

    我们使用的是 **Postman**，但任何 REST 工具都适用。 其他替代工具包括：带有 REST 插件的 **Visual Studio Code** 或 **Telerik Fiddler**。 

## <a name="download-postman-files"></a>下载 Postman 文件

克隆包含 Postman 集合和环境文件的 GitHub 存储库。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>配置 Postman

此部分配置 Postman。

### <a name="configure-the-environment"></a>配置环境 

1. 打开 **Postman**。
2. 在屏幕的右侧，选择“管理环境”选项。

    ![管理环境](./media/develop-with-postman/postman-import-env.png)
4. 从“管理环境”对话框中，单击“导入”。
2. 浏览到克隆 `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` 时下载的 `Azure Media Service v3 Environment.postman_environment.json` 文件。
6. **Azure Media Service v3 Environment** 环境已添加。

    > [!Note]
    > 使用从上面的“访问媒体服务 API”部分获得的值更新访问权限变量。

7. 双击所选的文件，并输入通过执行访问 API 步骤获得的值。
8. 关闭对话框。
9. 从下拉列表中选择“Azure Media Service v3 Environment”环境。

    ![选择环境](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>配置集合

1. 单击“导入”导入该集合文件。
1. 浏览到克隆 `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` 时下载的 `Media Services v3.postman_collection.json` 文件
3. 选择 **Media Services v3.postman_collection.json** 文件。

    ![导入文件](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>获取 Azure AD 令牌 

在开始操作 AMS v3 资源之前，需要获取并设置 Azure AD 令牌，以用于服务主体身份验证。

1. 在 Postman 的左窗口中，选择“步骤 1: 获取 AAD 身份验证令牌”。
2. 然后，选择“获取适用于服务主体身份验证的 Azure AD 令牌”。
3. 按“发送”。

    将会发送以下 **POST** 操作。

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 响应会返回此令牌并将“AccessToken”环境变量设置为令牌值。  

    ![获取 AAD 令牌](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="next-steps"></a>后续步骤

[使用 REST 流式传输文件](stream-files-tutorial-with-rest.md)。  
