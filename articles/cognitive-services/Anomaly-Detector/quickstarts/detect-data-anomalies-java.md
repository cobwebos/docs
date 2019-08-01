---
title: 快速入门：使用异常检测器 REST API 和 Java 检测时序数据的异常
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 能够以批或流数据的形式检测数据系列的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 001d53cbd7e2a57615ea3da71d128bd210a79921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565847"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>快速入门：使用异常检测器 REST API 和 Java 检测时序数据的异常

参考本快速入门可以开始使用异常检测器 API 的两种检测模式来检测时序数据的异常。 此 Java 应用程序发送两个包含 JSON 格式的时序数据的 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批的形式检测异常                        | JSON 响应包含时序数据中每个数据点的异常状态（和其他数据），以及检测到的任何异常所在的位置。 |
| 检测最新数据点的异常状态 | JSON 响应包含时序数据中最新数据点的异常状态（和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 Java 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>先决条件

- [Java&trade; 开发工具包 (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更高版本。

- 将这些库从 Maven 存储库导入
    - [以 Java 编写的 JSON](https://mvnrepository.com/artifact/org.json/json) 包
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 包

- 包含时序数据点的 JSON 文件。 可在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到本快速入门的示例数据。

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

2. 为订阅密钥和终结点创建变量。 下面是可用于异常检测的 URI。 稍后会将这些 URL 追加到服务终结点，以创建 API 请求 URL。

    |检测方法  |URI  |
    |---------|---------|
    |批量检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |对最新数据点进行检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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

3. 在 JSON 数据文件中读取

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `sendRequest()` 的新函数，该函数使用上面创建的变量。 然后，执行以下步骤。

2. 创建可以向 API 发送请求的 `CloseableHttpClient` 对象。 通过将终结点和异常检测器 URL 结合使用，将请求发送到 `HttpPost` 请求对象。

3. 使用请求的 `setHeader()` 函数将 `Content-Type` 标头设置为 `application/json`，然后将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

4. 使用请求的 `setEntity()` 函数来设置要发送的数据。

5. 使用客户端的 `execute()` 函数来发送请求，并将其保存到 `CloseableHttpResponse` 对象。

6. 创建 `HttpEntity` 对象，用于存储响应内容。 使用 `getEntity()` 获取内容。 如果响应不为空，请将其返回。

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

## <a name="detect-anomalies-as-a-batch"></a>以批的形式检测异常

1. 创建名为 `detectAnomaliesBatch()` 的方法，以批的形式检测整个数据中的异常。 调用在上面使用终结点、URL、订阅密钥和 JSON 数据创建的 `sendRequest()` 方法。 获取结果，并将其输出到控制台。

2. 如果响应包含 `code` 字段，请输出错误代码和错误消息。

3. 否则，请查找异常在数据集中的位置。 响应的 `isAnomaly` 字段包含一个布尔值，该值与给定的数据点是否为异常相关。 获取 JSON 数组，循环访问该数组，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

* 创建名为 `detectAnomaliesLatest()` 的方法，以检测数据集中最后一个数据点的异常状态。 调用在上面使用终结点、URL、订阅密钥和 JSON 数据创建的 `sendRequest()` 方法。 获取结果，并将其输出到控制台。

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时序数据并发送请求

1. 在应用程序的 main 方法中读入 JSON 文件，其中包含将要添加到请求中的数据。

2. 调用上面创建的两个异常检测函数。

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>示例响应

成功的响应以 JSON 格式返回。 单击以下链接在 GitHub 上查看 JSON 响应：
* [批量检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新数据点检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
