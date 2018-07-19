---
title: Azure 机器学习模型管理 Web 服务使用 | Microsoft Docs
description: 本文档介绍了在使用通过 Azure 机器学习中的模型管理部署的 Web 服务时涉及的步骤和概念。
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 4a49ccff68003cf7b81a7d945176992a2893d1ac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973169"
---
# <a name="consuming-web-services"></a>使用 Web 服务
将模型部署为实时 Web 服务后，可以从各种平台和应用程序向其发送数据并获取预测数据。 实时 Web 服务公开了一个用于获取预测数据的 REST API。 可以采用单行或多行格式向 Web 服务发送数据，从而一次获取一行或多行预测数据。

使用 [Azure 机器学习 Web 服务](model-management-service-deploy.md)，外部应用程序可通过对服务 URL 进行 HTTP POST 调用以同步与预测模型进行通信。 若要进行 Web 服务调用，客户端应用程序需要指定在部署预测时创建的 API 密钥，并将请求数据置于 POST 请求正文中。

> [!NOTE]
> 请注意，API 密钥仅在群集部署模式下可用。 本地 Web 服务没有密钥。

## <a name="service-deployment-options"></a>服务部署选项
对于生产方案和测试方案，都可以将 Azure 机器学习 Web 服务部署到基于云的群集，还可以将其部署到使用 Docker 引擎的本地工作站。 在两种情况下，预测模型的功能是相同的。 基于群集的部署提供了基于 Azure 容器服务的可伸缩和高性能解决方案，而本地部署可以用于调试。 

Azure 机器学习 CLI 和 API 提供了实用的命令，可用于在使用 ```az ml env``` 选项的情况下为服务部署创建和管理计算环境。 

## <a name="list-deployed-services-and-images"></a>列出已部署的服务和映像
可以使用 CLI 命令 ```az ml service list realtime -o table``` 列出当前已部署的服务和 Docker 映像。 请注意，此命令始终在当前计算环境的上下文中正常工作。 对于未设置为当前的环境，它不会显示其中部署的服务。 若要设置环境，请使用 ```az ml env set```。 

## <a name="get-service-information"></a>获取服务信息
成功部署 Web 服务后，使用以下命令获取服务 URL 和用于调用服务终结点的其他详细信息。 

```
az ml service usage realtime -i <web service id>
```

如果在部署时提供了服务 API 架构，则此命令将输出用于调用服务的服务 URL、所需请求标头、Swagger URL 和示例数据。

可以通过输入示例 CLI 命令及输入数据，直接从 CLI 对服务进行测试而不编写 HTTP 请求：

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>获取服务 API 密钥
若要获取 Web 服务密钥，请使用以下命令：

```
az ml service keys realtime -i <web service id>
```
创建 HTTP 请求时，在授权标头中使用该密钥："Authorization": "Bearer <key>"

## <a name="get-the-service-swagger-description"></a>获取服务 Swagger 说明
如果提供了服务 API 架构，则服务终结点将在 ```http://<ip>/api/v1/service/<service name>/swagger.json``` 公开 Swagger 文档。 Swagger 文档可以用来自动生成服务客户端以及浏览预期的输入数据和关于服务的其他详细信息。

## <a name="get-service-logs"></a>获取服务日志
若要了解服务行为并对问题进行诊断，可通过以下几种方式来检索服务日志：
- CLI 命令 ```az ml service logs realtime -i <service id>```。 此命令在群集和本地模式下都可以工作。
- 如果在部署时启用了服务日志记录，则还会将服务日志发送到 AppInsight。 CLI 命令 ```az ml service usage realtime -i <service id>``` 用于显示 AppInsight URL。 请注意，AppInsight 日志可能会延迟 2-5 分钟。
- 可以通过在使用 ```az ml env set``` 设置当前群集环境时连接的 Kubernetes 控制台查看群集日志
- 当服务在本地运行时，可以通过 Docker 引擎日志获取本地 Docker 日志。

## <a name="call-the-service-using-c"></a>使用 C# 调用服务
使用服务 URL 从 C# 控制台应用发送请求。 

1. 在 Visual Studio 中，创建一个新的控制台应用： 
    * 在菜单中，单击“文件”->“新建”->“项目”
    * 在 Visual Studio C# 下，单击“Windows 经典桌面”，然后选择“控制台应用”。
2. 输入 `MyFirstService` 作为项目名称，然后单击“确定”。
3. 在项目引用中，将引用设置为 `System.Net` 和 `System.Net.Http`。
4. 单击“工具”->“NuGet 包管理器”->“包管理器控制台”，然后安装 Microsoft.AspNet.WebApi.Client 包。
5. 打开 Program.cs 文件，将代码替换为以下代码：
6. 使用来自你的 Web 服务的信息更新 `SERVICE_URL` 和 `API_KEY` 参数。
7. 运行该项目。

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>使用 Python 调用 Web 服务
使用 Python 向你的实时 Web 服务发送请求。 

1. 将以下代码示例复制到一个新的 Python 文件。
2. 更新 data、url 和 api_key 参数。 对于本地 Web 服务，删除“Authorization”标头。
3. 运行代码。 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
