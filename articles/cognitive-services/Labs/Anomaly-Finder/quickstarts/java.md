---
title: 如何将异常查找器 API 与 Java 配合使用 - Microsoft 认知服务 | Microsoft Docs
description: 获取信息和示例代码，帮助用户快速开始使用 Java 和认知服务中的异常情况检测。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 8152c23e6c5332d243d851be56bab1e4085dbe5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366049"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>将异常查找器 API 与 Java 配合使用

本文提供了相关信息和示例代码，帮助用户快速开始将异常情况检测 API 与 Java 配合使用，从而获取时序数据的异常情况检测结果。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>通过使用 Java 的异常情况检测 API 获取异常点

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>时序数据示例

时序数据点示例如下。

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>分析数据，并获取异常点 Java 示例

若要运行该示例，请执行以下步骤：
1. 创建新的命令行应用。
2. 将 Main 类替换为以下代码（保留任何 `package` 语句）。
3. 将 `[YOUR_SUBSCRIPTION_KEY]` 值替换为有效订阅密钥。
4. 将 `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` 替换为示例或自己的数据点。
5. 将这些全局库从 Maven 存储库下载到项目中的 `lib` 目录：
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. 运行“Main”。

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### <a name="example-response"></a>示例响应

JSON 中返回了成功的响应。 示例响应如下。
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Java 应用](../tutorials/java-tutorial.md)
