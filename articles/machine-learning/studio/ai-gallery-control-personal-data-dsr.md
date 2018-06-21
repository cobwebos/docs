---
title: 从 Azure AI 库中查看和删除数据 | Microsoft Docs
description: 可以使用界面或 AI 库目录 API 从 Azure AI 库中导出和删除产品内用户数据。 本文介绍相关实现方法。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: bc6ffa912d7914c8662dbde623e04947540ac149
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658980"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>从 Azure AI 库中查看和删除产品内用户数据

可以使用界面或 AI 库目录 API 从 Azure AI 库中查看和删除产品内用户数据。 本文介绍了相关实现方法。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>使用 UI 在 AI 库中查看数据

你可以查看通过 Azure AI 库网站 UI 发布的项目。 用户可以查看已公开和未列出的解决方案、项目、试验及其他已发布的项目：

1.  登录到 [Azure AI 库](https://gallery.azure.ai/)。
2.  单击右上角的配置文件图片，然后单击帐户名称以加载配置文件页面。
3.  配置文件页面显示发布到库的所有项目，包括未列出的条目。

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>使用 AI 库目录 API 查看数据

可以通过 AI 库目录 API（可通过 https://catalog.cortanaanalytics.com/entities 访问）以编程方式查看所收集的数据。 若要查看数据，需要作者 ID。 若要通过目录 API 查看未列出的实体，则需要访问令牌。

目录响应以 JSON 格式返回。

### <a name="get-an-author-id"></a>获取作者 ID
作者 ID 基于发布到 Azure AI 库时使用的电子邮件地址。 它不会发生变化：

1.  登录到 [Azure AI 库](https://gallery.azure.ai/)。
2.  单击右上角的配置文件图片，然后单击帐户名称以加载配置文件页面。
3.  地址栏中的 URL 显示字母数字 ID，后跟 `authorId=`。 例如，对于 URL：`https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    作者 ID 为：`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>获取访问令牌

需要访问令牌才能通过目录 API 查看未列出的实体。 在没有访问令牌的情况下，用户仍然可以查看公共实体和其他用户信息。

若要获取访问令牌，需检查登录时浏览器对目录 API 发出的 HTTP 请求的 `DataLabAccessToken` 标头：

1.  登录到 [Azure AI 库](https://gallery.azure.ai/)。
2.  单击右上角的配置文件图片，然后单击帐户名称以加载配置文件页面。
3.  按 F12 打开浏览器的“开发人员工具”窗格，选择“网络”选项卡，然后刷新页面。 
4. 通过在“筛选器”文本框中键入字符串 *catalog*，根据该字符串筛选请求。
5.  在对 URL `https://catalog.cortanaanalytics.com/entities` 的请求中，找到 GET 请求并选择“标头”选项卡。向下滚动到“请求标头”部分。
6.  标头 `DataLabAccessToken` 下便是字母数字令牌。 为了确保数据安全，请不要共享此令牌。

### <a name="view-user-information"></a>查看用户信息
使用在之前步骤中获取的作者 ID，通过替换以下 URL 中的 `[AuthorId]` 来查看用户配置文件中的信息：

    https://catalog.cortanaanalytics.com/users/[AuthorID]

例如，此 URL 请求：
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

返回如下响应：

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>查看公共实体

目录 API 将有关已发布实体的信息存储到 Azure AI 库中，你也可以直接在 [AI 库网站](https://gallery.azure.ai/)上查看该库。 

若要查看已发布的实体，请访问以下 URL，并将 `[AuthorId]` 替换为在上面的[获取作者 ID](#get-an-author-ID) 中获取的作者 ID。

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

例如：

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>查看未列出的实体和公共实体

此查询仅显示公共实体。 若要查看所有实体，包括未列出的实体，请提供从前一部分获取的访问令牌。

1.  使用 [Postman](https://www.getpostman.com) 之类的工具，按[获取访问令牌](#get-your-access-token)中所述创建对目录 URL 的 HTTP GET 请求。
2.  创建名为 `DataLabAccessToken` 的 HTTP 请求标头，并将值设置为访问令牌。
3.  提交 HTTP 请求。

> [!TIP]
> 如果未列出的实体没有出现在目录 API 的响应中，则表示该用户的访问令牌可能无效或者已过期。 从 Azure AI 库注销，然后重复[获取访问令牌](#get-your-access-token)中的步骤以续订令牌。 
