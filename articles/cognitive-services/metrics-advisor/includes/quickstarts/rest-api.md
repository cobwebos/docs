---
title: 指标监视器 REST API 快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 82eec57b30e177f75a3ac689dc096dfea54c6717
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943394"
---
## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 获得 Azure 订阅后，<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="“创建指标顾问”资源"  target="_blank">在 Azure 门户中创建“指标顾问”资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，以部署“指标顾问”实例。  
* 最新版本的 [cURL](https://curl.haxx.se/)。 本文使用了 [cURL 文档](https://curl.haxx.se/docs/manpage.html)中所述的多个命令行开关。
    * 以下 BASH 示例使用 `\` 行继续符。 如果你的控制台或终端使用不同的行继续符，请使用此字符。

> [!TIP]
> * 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-rest-api-samples/) 上找到使用 REST API 的 Python 示例。
> * 部署指标顾问资源可能需要 10 到 30 分钟。 部署成功后，单击“转到资源”。
> * 部署完成后，可以开始通过 Web 门户和 REST API 使用指标顾问实例。 可以在创建的资源中找到相应的两个 URL。
> * 需要从创建的资源中获取密钥和终结点以开始使用该服务，可以在资源中的“密钥和终结点”中找到。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。

## <a name="add-a-data-feed-from-a-sample-or-data-source"></a>从示例或数据源添加数据馈送

若要开始监视时序数据，需要添加一个数据馈送。 若要添加数据馈送，需要根据数据源类型和参数提供数据架构。 将以下 JSON 请求正文保存到名为 body.json 的文件中，然后运行 cURL 命令。

```json
{
        "dataSourceType": "SqlServer",
        "dataFeedName": "test_data_feed_00000001",
        "dataFeedDescription": "",
        "dataSourceParameter": {
            "connectionString": "Server=ad-sample.database.windows.net,1433;Initial Catalog=ad-sample;Persist Security Info=False;User ID=adreadonly;Password=Readonly@2020;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
            "query": "select * from adsample3 where Timestamp = @StartTime"
        },
        "granularityName": "Daily",
        "granularityAmount": 0,
        "metrics": [
            {
                "metricName": "revenue",
                "metricDisplayName": "revenue",
                "metricDescription": ""
            },
            {
                "metricName": "cost",
                "metricDisplayName": "cost",
                "metricDescription": ""
            }
        ],
        "dimension": [
            {
                "dimensionName": "city",
                "dimensionDisplayName": "city"
            },
            {
                "dimensionName": "category",
                "dimensionDisplayName": "category"
            }
        ],
        "timestampColumn": "timestamp",
        "dataStartFrom": "2020-06-01T00:00:00.000Z",
        "startOffsetInSeconds": 0,
        "maxConcurrency": -1,
        "minRetryIntervalInSeconds": -1,
        "stopRetryAfterInSeconds": -1,
        "needRollup": "NoRollup",
        "fillMissingPointType": "SmartFilling",
        "fillMissingPointValue": 0,
        "viewMode": "Private",
        "admins": [
            "xxx@microsoft.com"
        ],
        "viewers": [
        ],
        "actionLinkTemplate": ""
}
```

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥和 JSON 值编辑此命令。

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/datafeeds \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>示例响应

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/anomalydetector-ee/v1.0/datafeeds/b5921405-8001-42b2-8746-004ddeeb780d
x-envoy-upstream-service-time: 564
apim-request-id: 4e4fe70b-d663-4fb7-a804-b9dc14ba02a3
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Thu, 03 Sep 2020 18:29:27 GMT
```
在上面的响应中，“位置”标头是所创建的数据馈送的 URL，其中包含“dataFeedID” 。 

使用上述 URL，可以查询在上一步中创建的数据馈送的详细信息。 （在以下步骤中，我们将使用数据馈送信息中的“metricID”）

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/datafeeds/REPLACE-WITH-YOUR-DATA-FEED-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

#### <a name="example-response"></a>示例响应

```
{
   "dataFeedId":"90919c03-be13-4efa-86e5-aa9dc72764ce",
   "dataFeedName":"test_data_feed_00000007",
   "metrics":[
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "metricName":"cost",
         "metricDisplayName":"cost",
         "metricDescription":""
      },
      {
         "metricId":"82bbc63d-3739-4d57-b190-accb69721b6a",
         "metricName":"revenue",
         "metricDisplayName":"revenue",
         "metricDescription":""
      }
   ],
   "dimension":[
      {
         "dimensionName":"category",
         "dimensionDisplayName":"category"
      },
      {
         "dimensionName":"city",
         "dimensionDisplayName":"city"
      }
   ],
   "dataStartFrom":"2020-06-01T00:00:00Z",
   "dataSourceType":"SqlServer",
   "timestampColumn":"timestamp",
   "startOffsetInSeconds":0,
   "maxQueryPerMinute":30.0,
   "granularityName":"Daily",
   "granularityAmount":null,
   "allUpIdentification":null,
   "needRollup":"NoRollup",
   "fillMissingPointType":"SmartFilling",
   "fillMissingPointValue":0.0,
   "rollUpMethod":"None",
   "rollUpColumns":[
      
   ],
   "dataFeedDescription":"",
   "stopRetryAfterInSeconds":-1,
   "minRetryIntervalInSeconds":-1,
   "maxConcurrency":-1,
   "viewMode":"Private",
   "admins":[
      "zhli2@microsoft.com"
   ],
   "viewers":[
      
   ],
   "creator":"bowgong@microsoft.com",
   "status":"Active",
   "createdTime":"2020-09-08T08:39:28Z",
   "isAdmin":true,
   "actionLinkTemplate":"",
   "dataSourceParameter":{
      "connectionString":"Server=ad-sample.database.windows.net,1433;Initial Catalog=ad-sample;Persist Security Info=False;User ID=adreadonly;Password=Readonly@2020;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
      "query":"select * from adsample3 where Timestamp = @StartTime"
   }
}
```



## <a name="check-ingestion-status"></a>检查引入状态

添加数据馈送后，若要检查引入作业的进度，可以检查其状态。 将以下 JSON 请求正文保存到名为 body.json 的文件中，然后运行 cURL 命令。

```json
{
  "startTime": "2020-01-01T00:00:00.0000000+00:00",
  "endTime": "2020-01-04T00:00:00.0000000+00:00"
}
```

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥、JSON 值和 JSON 大小编辑此命令。


```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/datafeeds/REPLACE-WITH-YOUR-DATA-FEED-ID/ingestionStatus/query \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```


#### <a name="example-response"></a>示例响应

```json
{
  "@nextLink": "https://demo.example.com/datafeeds/01234567-8901-2345-6789-012345678901/ingestionStatus/query?$skip=3&$top=1",
  "value": [
    {
      "timestamp": "2020-09-03T00:00:00.0000000+00:00",
      "status": "Running",
      "message": ""
    },
    {
      "timestamp": "2020-09-02T00:00:00.0000000+00:00",
      "status": "Succeeded",
      "message": ""
    },
    {
      "timestamp": "2020-09-01T00:00:00.0000000+00:00",
      "status": "Failed",
      "message": "No valid record pulled from source for current timestamp 2020-01-01T00:00:00Z"
    }
  ]
}
```

##  <a name="configure-anomaly-detection-configuration"></a>配置异常情况检测配置

虽然默认配置会自动应用于每个指标，但可以调整对数据用的检测模式。 将以下 JSON 请求正文保存到名为 body.json 的文件中，然后运行 cURL 命令。

```json
{
        "name": "test_detection_config0000000001",
        "description": "string",
        "metricId": "8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
        "wholeMetricConfiguration": {
            "smartDetectionCondition": {
                "sensitivity": 100,
                "anomalyDetectorDirection": "Both",
                "suppressCondition": {
                    "minNumber": 1,
                    "minRatio": 1
                }
            }
        },
        "dimensionGroupOverrideConfigurations": [
        ],
        "seriesOverrideConfigurations": [
        ]
}
```

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥、JSON 值和 JSON 大小编辑此命令。

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/enrichment/anomalyDetection/configurations \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>示例响应

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/anomalydetector-ee/v1.0/enrichment/anomalyDetection/configurations/6a977d61-f0f5-488a-a162-2feb4643ae09
x-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
x-envoy-upstream-service-time: 253
apim-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 09:50:38 GMT
```

在上述“Location”标头中，包含新创建的资源（检测配置）的 URL。 

使用上述“Location”标头的 URL，可以查询已创建的检测配置（在以下步骤中，将在响应内容中使用“anomalyDetectionConfigurationId” ）

```
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/enrichment/anomalyDetection/configurations/REPLACE-WITH-YOUR-DETECTION-CONFIGURATION-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

#### <a name="example-response"></a>示例响应

```json
{
   "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
   "name":"test_detection_config0000000001",
   "description":"string",
   "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
   "wholeMetricConfiguration":{
      "smartDetectionCondition":{
         "sensitivity":100.0,
         "anomalyDetectorDirection":"Both",
         "suppressCondition":{
            "minNumber":1,
            "minRatio":1.0
         }
      }
   },
   "dimensionGroupOverrideConfigurations":[
      
   ],
   "seriesOverrideConfigurations":[
      
   ]
}
```



### <a name="configure-alert-configuration"></a>配置警报配置

在配置警报之前，需要创建一个将用于通知警报的挂钩。 有两种方法可以在触发警报时获取通知，即 Webhook 和电子邮件。 创建挂钩时，可以在挂钩配置中将其中之一指定为挂钩类型。

将以下 JSON 请求正文保存到名为 body.json 的文件中，然后运行 cURL 命令。

```json
{
        "hookType": "Webhook",
        "hookName": "test_web_hook000001",
        "description": "",
        "externalLink": "",
        "hookParameter": {
            "endpoint": "https://www.xxx.com/aaa",
            "username": "",
            "password": ""
        }
}
```

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥、JSON 值和 JSON 大小编辑此命令。

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/hooks \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>示例响应

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/anomalydetector-ee/v1.0/hooks/34d677bd-0875-4760-8bf6-24d48abde7c3
x-request-id: 7b6cc1a6-02cb-405b-bee3-174fdae0a7d2
x-envoy-upstream-service-time: 1640
apim-request-id: 7b6cc1a6-02cb-405b-bee3-174fdae0a7d2
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 10:37:59 GMT
```

使用上述“Location”标头中的 URL，可以查询已创建的 Webhook。

```
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/hooks/REPLACE-WITH-YOUR-HOOK-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

#### <a name="example-response"></a>示例响应

```json
{
   "hookId":"34d677bd-0875-4760-8bf6-24d48abde7c3",
   "hookName":"test_web_hook000001",
   "hookType":"Webhook",
   "externalLink":"",
   "description":"",
   "admins":[
      "bowgong@microsoft.com"
   ],
   "hookParameter":{
      "endpoint":"https://www.xxx.com/aaa",
      "username":"",
      "password":"",
      "headers":{
         

      },
      "certificateKey":"",
      "certificatePassword":""

   }
}
```

通过配置警报配置，可以指定可用于触发警报的检测条件。 将以下 JSON 请求正文保存到名为 body.json 的文件中，然后运行 cURL 命令。

```json
{
        "name": "test_alert_config00000001",
        "description": "",
        "crossMetricsOperator": "AND",
        "hookIds": [
           "34d677bd-0875-4760-8bf6-24d48abde7c3" 
        ],
        "metricAlertingConfigurations": [
            {
                "anomalyDetectionConfigurationId": "6a977d61-f0f5-488a-a162-2feb4643ae09",
                "anomalyScopeType": "All",
                "severityFilter": {
                    "minAlertSeverity": "Low",
                    "maxAlertSeverity": "High"
                },
                "snoozeFilter": {
                    "autoSnooze": 0,
                    "snoozeScope": "Metric",
                    "onlyForSuccessive": true
                },
            }
        ]
}
```

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥、JSON 值和 JSON 大小编辑此命令。

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/alert/anomaly/configurations \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>示例响应

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/anomalydetector-ee/v1.0/alert/anomaly/configurations/40004c91-6996-47c0-b8c8-fd20a8f4f0ab
x-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
x-envoy-upstream-service-time: 253
apim-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 09:50:38 GMT
```

在上述“Location”标头中，包含新创建的资源（检测配置）的 URL。 

使用上述“Location”标头中的 URL，可以查询已创建的警报配置。 （在以下步骤中，将在警报配置中使用“anomalyAlertingConfigurationId”）

```
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

#### <a name="example-response"></a>示例响应

```json
{
   "anomalyAlertingConfigurationId":"40004c91-6996-47c0-b8c8-fd20a8f4f0ab",
   "name":"test_alert_config00000001",
   "description":"",
   "hookIds":[
      "34d677bd-0875-4760-8bf6-24d48abde7c3"
   ],
   "metricAlertingConfigurations":[
      {
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "anomalyScopeType":"All",
         "negationOperation":false,
         "severityFilter":{
            "minAlertSeverity":"Low",
            "maxAlertSeverity":"High"
         },
         "snoozeFilter":{
            "autoSnooze":0,
            "snoozeScope":"Metric",
            "onlyForSuccessive":true
         }
      }
   ]
}
```

### <a name="query-anomaly-detection-results"></a>查询异常情况检测结果

可以通过不同的方式获取检测结果。 例如，可以使用创建的检测配置定期主动查询检测结果，也可以通过警报获取通知，然后可以使用此警报来查询相应的异常。

以下示例中演示了如何查询警报，并使用此警报来查询与其相关的异常。

#### <a name="query-alert"></a>查询警报

可以使用在上述步骤中创建的警报配置来查询警报。

```
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/anomalydetector-ee/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID/alerts/query \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

#### <a name="example-response"></a>示例响应

```json
{
   "value":[
      {
         "alertId":"17465dcc000",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.532Z",
         "modifiedTime":"2020-09-08T19:12:46.588Z"
      }
   ],
   "@nextLink":null
}
```

在上述响应中，我们收到了一个警报。 使用此“alertID”，我们可以查询导致此警报的所有相关异常。

（获取警报的另一种方法是配置 Webhook，并在发现警报后被动收到警报）

#### <a name="query-anomalies-using-alertid"></a>使用 alertID 查询异常

```
curl https://REPLACE-WITH-YOUR-ENDPOINT/anomalydetector-ee/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID/alerts/REPLACE-WITH-YOUR-ALERTID/anomalies \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

#### <a name="example-response"></a>示例响应

```json
{
   "value":[
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Caucasian Fir"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Animals by number of neurons",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Animals by number of neurons",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Chickadee",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Chickadee",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      }
   ],
   "@nextLink":null
}
```
