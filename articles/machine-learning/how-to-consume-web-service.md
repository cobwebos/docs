---
title: 为部署为 Web 服务的模型创建客户端
titleSuffix: Azure Machine Learning
description: 了解如何调用从 Azure 机器学习部署模型时生成的 Web 服务终结点。 该终结点公开一个 REST API，可以调用它来执行模型推理。 使用所选的编程语言为此 API 创建客户端。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 7aa17a7a96bffd0cd6f68f6187038aabd72b8cbd
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542155"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>使用部署为 Web 服务的 Azure 机器学习模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

将 Azure 机器学习模型部署为 Web 服务可创建 REST API 终结点。 可将数据发送到此终结点，并接收模型返回的预测。 本文档介绍了如何使用 C#、Go、Java 和 Python 为 Web 服务创建客户端。

将模型部署到本地环境、Azure 容器实例、Azure Kubernetes 服务或现场可编程门阵列 (FPGA) 时，你将创建一个 Web 服务。 你将使用 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 检索用来访问 Web 服务的 URI。 如果启用了身份验证，则还可以使用该 SDK 来获取身份验证密钥或令牌。

用于创建使用机器学习 Web 服务的客户端的常规工作流为：

1. 使用 SDK 获取连接信息。
1. 确定模型使用的请求数据的类型。
1. 创建调用 Web 服务的应用程序。

> [!TIP]
> 本文档中的示例是手动创建的，没有使用 OpenAPI (Swagger) 规范。 如果已为部署启用了 OpenAPI 规范，则可以使用 [swagger-codegen](https://github.com/swagger-api/swagger-codegen) 之类的工具为服务创建客户端库。

## <a name="connection-information"></a>连接信息

> [!NOTE]
> 使用 Azure 机器学习 SDK 获取 Web 服务信息。 这是一个 Python SDK。 可以使用任何语言来为服务创建客户端。

[azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 类提供了创建客户端所需的信息。 创建客户端应用程序时，以下 `Webservice` 属性非常有用：

* `auth_enabled` - 如果启用了密钥身份验证，则为 `True`；否则为 `False`。
* `token_auth_enabled` - 如果启用了令牌身份验证，则为 `True`；否则为 `False`。
* `scoring_uri` - REST API 地址。
* `swagger_uri` - OpenAPI 规范的地址。 如果已启用自动生成架构，则可以使用此 URI。 有关详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

可通过三种方式检索已部署的 Web 服务的此信息：

* 部署模型时，会返回包含有关服务的信息的 `Webservice` 对象：

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* 可以使用 `Webservice.list` 检索工作区中为模型部署的 Web 服务列表。 可以添加筛选器，以缩小返回的信息列表范围。 有关可以筛选的对象的详细信息，请参阅 [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) 参考文档。

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* 如果知道已部署的服务的名称，可以创建 `Webservice` 的新实例，并以参数的形式提供工作区和服务名称。 新对象包含有关已部署的服务的信息。

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>受保护的 Web 服务

如果已使用 TLS/SSL 证书保护部署的 Web 服务，则可以使用评分或 swagger URI 通过 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 连接到该服务。 HTTPS 对客户端和 Web 服务之间的通信进行加密来帮助保护两者之间的通信。 加密使用[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有时仍称为安全套接字层 (SSL)，这是 TLS 的前身。

> [!IMPORTANT]
> Azure 机器学习部署的 Web 服务仅支持 TLS 版本 1.2。 创建客户端应用程序时，请确保它支持此版本。

有关详细信息，请参阅[使用 TLS 通过 Azure 机器学习来保护 Web 服务](how-to-secure-web-service.md)。

### <a name="authentication-for-services"></a>服务的身份验证

Azure 机器学习提供了两种方法来控制对 Web 服务的访问。

|身份验证方法|ACI|AKS|
|---|---|---|
|键|默认已禁用| 默认情况下启用|
|令牌| 不可用| 默认已禁用 |

将请求发送到由密钥或令牌保护的服务时，请使用 __Authorization__ 标头来传递密钥或令牌。 密钥或令牌的格式必须为 `Bearer <key-or-token>`，其中 `<key-or-token>` 为密钥或令牌值。

密钥与标记之间的主要区别在于，**密钥是静态的，可以手动重新生成**，并且**令牌需要在过期时刷新**。 Azure 容器实例和 Azure Kubernetes 服务部署的 Web 服务支持基于密钥的身份验证，而基于令牌的身份验证仅能用于 Azure Kubernetes 服务部署****。 请参阅身份验证[操作说明](how-to-setup-authentication.md#web-service-authentication)，了解更多信息和特定代码示例。


#### <a name="authentication-with-keys"></a>使用密钥进行身份验证

为部署启用身份验证时，会自动创建身份验证密钥。

* 部署到 Azure Kubernetes 服务时，会默认启用身份验证。
* 部署到 Azure 容器实例时，会默认禁用身份验证。

若要控制身份验证，请在创建或更新部署时使用 `auth_enabled` 参数。

如果已启用身份验证，可以使用 `get_keys` 方法来检索主要和辅助身份验证密钥：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如需重新生成密钥，请使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)。

#### <a name="authentication-with-tokens"></a>使用令牌进行身份验证

当为 Web 服务启用了令牌身份验证时，用户必须向 Web 服务提供 Azure 机器学习 JWT 令牌才能访问。 

* 部署到 Azure Kubernetes 服务时，默认情况下会禁用令牌身份验证。
* 部署到 Azure 容器实例时，不支持令牌身份验证。

若要控制令牌身份验证，请在创建或更新部署时使用 `token_auth_enabled` 参数。

如果启用了令牌身份验证，可以使用 `get_token` 方法来检索持有者令牌以及该令牌的到期时间：

```python
token, refresh_by = service.get_token()
print(token)
```

如果安装了 [Azure CLI 和机器学习扩展](reference-azure-machine-learning-cli.md)，则可使用以下命令获取令牌：

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> 目前，检索令牌的唯一方法是使用 Azure 机器学习 SDK 或 Azure CLI 机器学习扩展。

需要在令牌的 `refresh_by` 时间后请求一个新令牌。 

## <a name="request-data"></a>请求数据

REST API 预期请求正文是采用以下结构的 JSON 文档：

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> 数据结构需要符合服务中评分脚本和模型的预期。 评分脚本在将数据传递到模型之前可以修改数据。

例如，[在笔记本中训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)示例中的模型预期存在由 10 个数字构成的数组。 此示例的评分脚本从请求创建一个 Numpy 数组，并将其传递给模型。 以下示例演示此服务预期的数据：

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

Web 服务可以接受一个请求中的多个数据集。 它会返回包含响应数组的 JSON 文档。

### <a name="binary-data"></a>Binary data

有关如何在服务中启用对二进制数据的支持的信息，请参阅[二进制数据](how-to-deploy-advanced-entry-script.md#binary-data)。

> [!TIP]
> 启用二进制数据支持这一操作是在部署的模型所使用的 score.py 文件中实现的。 从客户端中，使用编程语言的 HTTP 功能。 例如，以下代码片段将 JPG 文件的内容发送到 Web 服务：
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>跨域资源共享 (CORS)

有关在服务中启用 CORS 支持的信息，请参阅[跨源资源共享](how-to-deploy-advanced-entry-script.md#cors)。

## <a name="call-the-service-c"></a>调用服务 (C#)

此示例演示如何使用 C# 调用[在笔记本中训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)示例创建的 Web 服务：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

返回的结果类似于以下 JSON 文档：

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>调用服务 (Go)

此示例演示如何使用 Go 调用[在笔记本中训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)示例创建的 Web 服务：

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

返回的结果类似于以下 JSON 文档：

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>调用服务 (Java)

此示例演示如何使用 Java 调用[在笔记本中训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)示例创建的 Web 服务：

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

返回的结果类似于以下 JSON 文档：

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>调用服务 (Python)

此示例演示如何使用 Python 调用[在笔记本中训练](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)示例创建的 Web 服务：

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

返回的结果类似于以下 JSON 文档：

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Web 服务架构（OpenAPI 规范）

如果在部署中使用了自动生成架构，则可以通过使用 [swagger_uri 属性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)获取服务的 OpenAPI 规范的地址。 （例如 `print(service.swagger_uri)`。）使用 GET 请求，或在浏览器中打开 URI 以检索规范。

以下 JSON 文档是为部署生成的架构（OpenAPI 规范）示例：

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

有关详细信息，请参阅 [OpenAPI 规范](https://swagger.io/specification/)。

若要了解可根据规范创建客户端库的实用工具，请参阅 [swagger-codegen](https://github.com/swagger-api/swagger-codegen)。


> [!TIP]
> 部署服务后，可以检索架构 JSON 文档。 使用部署的 Web 服务中的 [swagger_uri 属性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)（例如 `service.swagger_uri`）获取本地 Web 服务的 Swagger 文件的 URI。

## <a name="consume-the-service-from-power-bi"></a>通过 Power BI 使用服务

Power BI 支持使用 Azure 机器学习 Web 服务，以通过预测来扩充 Power BI 中的数据。 

若要生成支持在 Power BI 中使用的 Web 服务，架构必须支持 Power BI 所需的格式。 [了解如何创建 Power BI 支持的架构](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script)。

在部署 Web 服务后，可通过 Power BI 数据流来使用它。 [了解如何通过 Power BI 使用 Azure 机器学习 Web 服务](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="next-steps"></a>后续步骤

若要查看 Python 和深度学习模型的实时评分的参考体系结构，请转至 [Azure 体系结构中心](/azure/architecture/reference-architectures/ai/realtime-scoring-python)。
