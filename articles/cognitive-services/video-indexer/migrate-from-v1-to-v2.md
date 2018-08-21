---
title: 从 Azure 视频索引器 API v1 迁移到 v2 | Microsoft Docs
description: 本主题介绍如何从 Azure 视频索引器 API v1 迁移到 v2。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: b1737960a4142f5c0d949ce8c2524c34fe9cd79e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "40187362"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>从视频索引器 API v1 迁移到 v2

> [!Note]
> 视频索引器 V1 API 已于 2018 年 8 月 1 日弃用。 从现在起，应该使用视频索引器 v2 API。 <br/>若要使用视频索引器 v2 API 进行开发，请参阅[此处](https://api-portal.videoindexer.ai/)的说明。 

本文介绍在 v2 中引入的更改。  

## <a name="api-changes"></a>API 更改

### <a name="authorization-and-operations"></a>授权和操作

在 v2 版本中，视频索引器更改了 API 的身份验证和授权模型。 有两组 API： 

* 授权 
* 操作

**授权** API 用于获取调用**操作** API 的访问令牌。 **操作** API 包含所有视频索引器 API，例如“上传视频”API、“获取见解”API 以及其他操作 API。

[订阅](video-indexer-get-started.md)**授权** API 后，即可通过传递订阅密钥来获取访问令牌（就像在 v1 中所做的那样）。

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>获取和使用操作 API 的访问令牌

调用**操作** API 后，不会再次使用订阅密钥， 而是会传递**授权** API 获取的访问令牌。 

每个请求都应该有一个有效的令牌，与你要调用的 API 的访问级别相匹配。 例如，对你的用户的操作（如获取你的帐户）需要用户访问令牌。 在帐户级别进行的操作（如列出所有视频）需要帐户访问令牌。 对视频进行的操作（如重新编制视频的索引）需要帐户访问令牌或视频访问令牌。

为简化操作，可以使用**授权** API > **GetAccounts** 来获取帐户，而不是先获取用户令牌。 还可以请求获取具有有效令牌的帐户，以便跳过附加的调用即可获取帐户令牌。

有关不同访问令牌的详细信息，请参阅[使用 Azure 视频索引器 API](video-indexer-use-apis.md)。

### <a name="locations"></a>位置

每次调用 API 时，应包括视频索引器帐户的位置。 如果没有位置或位置错误，API 调用会失败。

下表中所述的值适用。 “参数值”是使用 API 时传递的值。

|**Name**|**参数值**|**说明**|
|---|---|---|
|试用|试用|表示试用帐户。 例如：https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English。|
|美国西部|westus2|表示 Azure 美国西部 2 区域。  例如：https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English。|
|北欧 |northeurope|表示 Azure 北欧区域。 例如：https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English。 |
|东亚|eastasia|表示 Azure 东亚区域。 例如：https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English。|

### <a name="data-model"></a>数据模型

视频索引器现在可以通过简化的数据模型来提供清晰得多的见解。 若要详细了解 v2 API 生成的输出，请参阅[探讨 v2 API 生成的视频索引器输出](video-indexer-output-json-v2.md)。

### <a name="swagger"></a>Swagger

视频索引器 API 定义已进行相应的更新，可以通过 [API 门户](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token)下载。


### <a name="v1-vs-v2-examples"></a>V1 和 V2 示例的比较

新的 V2 API 涉及 3 个主要参数：

1. [LOCATION] - 如上所述。 可用选项：trial、westus2、northeurope 或 eastasia。
2. [YOUR_ACCOUNT_ID] - 你的帐户的 GUID ID。 在获取所有帐户时检索（如下所述）。
3. [YOUR_VIDEO_ID] - 视频的 ID（例如“d4fa369abc”）。 在上传视频或搜索视频时返回。

#### <a name="uploading-a-video-in-v1"></a>在 V1 中上传视频：

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>在 V2 中上传视频：

1. 获取上传请求的访问令牌：

  可以获取所有帐户及其访问令牌：

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  也可以获取特定的帐户访问令牌：
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. 上传视频：

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>在 V1 中获取见解：

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>在 V2 中获取见解：

1. 使用帐户访问令牌，或获取视频级别访问令牌：

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. 获取见解：

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>在 V1 中获取视频处理状态：

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>在 V2 中获取视频处理状态：

在 API v2 中，处理状态是在“获取视频索引”API 中返回的。

1. 使用帐户访问令牌，或获取视频级别访问令牌：

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. 获取见解：

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>后续步骤

[使用 Azure 视频索引器 API](video-indexer-use-apis.md)

