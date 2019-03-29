---
title: 快速入门：使用异常检测器 REST API 和 Java 时序数据中检测异常 |Microsoft Docs
description: 使用异常检测器 API 来检测异常数据系列中，作为一个批或针对流式处理数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 06cb4d32359014f3cbc67ed1f75988c794e6599e
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619505"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>快速入门：使用异常检测器 REST API 和 Java 时序数据中检测异常

使用本快速入门教程开始使用异常检测器 API 的两种检测模式来检测异常的时序数据。 此 Java 应用程序发送包含 JSON 格式的时间序列数据的两个 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 作为一批中检测异常                        | JSON 响应包含用于时间序列数据和任何检测到的异常的位置中的每个数据点异常状态 （和其他数据）。 |
| 检测到异常情况状态的最新的数据点 | JSON 响应包含用于时间序列数据的最新数据点的异常状态 （和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 Java 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>必备组件

- [Java&trade;开发 Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本。

- 从 Maven 存储库导入这些库
    - [在 Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json)包
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)包

- JSON 文件包含时间序列数据点。 本快速入门教程的示例数据，可[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库。

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. 为你的订阅密钥和你的终结点创建变量。 下面是可用于异常情况检测的 Uri。 这些将追加到你的服务终结点更高版本才能创建 API 请求 Url。

    |检测方法  |URI  |
    |---------|---------|
    |批处理检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新的数据点上的检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. JSON 数据文件中读取

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>创建一个函数来发送请求

1. 创建一个名为的新函数`sendRequest()`采用上面创建的变量。 然后，执行以下步骤。

2. 创建`CloseableHttpClient`可以向 API 发送请求的对象。 将请求发送到`HttpPost`通过合并你的终结点和异常情况检测程序 URL 的请求对象。

3. 使用请求的`setHeader()`函数来设置`Content-Type`标头`application/json`，并添加你的订阅密钥的`Ocp-Apim-Subscription-Key`标头。

4. 使用请求的`setEntity()`要发送的数据的函数。   

5. 使用客户端`execute()`函数发送请求，并将其保存到`CloseableHttpResponse`对象。 

6. 创建`HttpEntity`对象来存储响应内容。 获取与内容`getEntity()`。 如果响应不为空，则将其返回。

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>作为一批中检测异常

1. 创建一个名为方法`detectAnomaliesBatch()`来检测异常在整个数据作为一个批。 调用`sendRequest()`上面创建的终结点、 url、 订阅密钥和 json 数据的方法。 获取结果，并将其打印到控制台。

2. 在数据集中找到异常的位置。 响应的`isAnomaly`字段包含与给定的数据点是否异常的布尔值。 获取 JSON 数组并循环访问它，打印的任何索引`true`值。 如果任何发现，这些值对应于异常的数据点的索引。

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测到异常情况状态的最新的数据点

* 创建一个名为方法`detectAnomaliesLatest()`来检测数据集中的最后一个数据点的异常状态。 调用`sendRequest()`上面创建的终结点、 url、 订阅密钥和 json 数据的方法。 获取结果，并将其打印到控制台。

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时间系列数据并发送请求

1. 在您的应用程序，在包含将添加到请求的数据的 JSON 文件中读取的主要方法。

2. 调用上面创建的两个异常情况检测函数。
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>示例响应

JSON 格式返回成功的响应。 单击以下链接，查看 GitHub 上的 JSON 响应：
* [示例批处理检测响应](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [示例最新点检测响应](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)