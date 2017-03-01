---
title: "使用 API 检查器跟踪调用 - Azure API 管理 | Microsoft 文档"
description: "了解如何使用 API 检查器跟踪 Azure API 管理中的调用。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: a9d4d3be7f046af975f6dc25670070204848588c

---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>如何使用 API 检查器跟踪 Azure API 管理中的调用
API 管理提供了一个 API 检查器工具，帮助进行调试和故障诊断 API。 API 检查器可以编程方式使用，还可以直接从开发人员门户中使用。 

除了跟踪操作，API 检查器还跟踪[策略表达式](https://msdn.microsoft.com/library/azure/dn910913.aspx)评估。 有关演示，请参阅 [Cloud Cover 第 177 集：更多 API 管理功能](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)并快进到 21:00。

本指南提供使用 API 检查器的演练。

> [!NOTE]
> 仅为包含属于[管理员](api-management-howto-create-groups.md)帐户的订阅密钥的请求生成并提供 API 检查器跟踪。
> 
> 

## <a name="trace-call"> </a> 使用 API 检查器跟踪调用
若要使用 API 检查器，请将 **ocp-apim-trace: true** 请求标头添加到操作调用，然后使用 **ocp-apim-trace-location** 响应标头指定的 URL 下载并检查跟踪。 这可以编程方式完成，也可以直接从开发人员门户完成。

本文介绍如何使用 API 检查器借助在[管理第一个 API](api-management-get-started.md)入门教程中配置的 Basic Calculator API 跟踪操作。 如果尚未完成该教程，只需几分钟即可导入 Basic Calculator API，或者可以使用所选的另一个 API，例如 Echo API。 每个预先配置 Echo API 的 API 管理服务实例，都可用于试验和了解 API 管理。 Echo API 返回任何发送给它的输入。 要使用它，您可以调用任何 HTTP 谓词，并且返回的值将只是您发送的内容。 

若要开始，请在 API 管理服务的 Azure 门户中单击“开发人员门户”。 可以直接从开发人员门户调用操作，这样可以方便地查看和测试 API 的操作。

> 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Get started with Azure API Management]教程中的[创建 API 管理服务实例][Create an API Management service instance]。
> 
> 

![API 管理开发人员门户][api-management-developer-portal-menu]

从顶部菜单中单击“API”，然后单击“Basic Calculator”。

![Echo API][api-management-api]

单击“尝试”尝试“添加两个整数”操作。

![试用][api-management-open-console]

保留默认参数值，并从“订阅密钥”下拉列表中选择要使用的产品的订阅密钥。

默认情况下，在开发人员门户中，“Ocp-Apim-Trace”标头已设置为“true”。 此标头配置是否生成跟踪。

![发送][api-management-http-get]

单击“发送”调用该操作。

![发送][api-management-send-results]

响应标头将是 **ocp-apim-trace-location**，值类似于下面的示例。

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

可以从指定的位置下载和查看跟踪，如下一步中所示。 请注意：仅存储最近 100 个日志条目，并会在循环中重用日志位置。 因此，如果启用跟踪进行 100 个以上的调用，最终将就地开始覆盖最初的跟踪。

## <a name="inspect-trace"> </a>检查跟踪
若要查看在跟踪中的值，请从 **ocp-apim-trace-location** URL 下载跟踪文件。 它是采用 JSON 格式的文本文件，并包含类似下面示例的条目。

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"> </a>后续步骤
* 观看 [Cloud Cover Episode 177：更多 More API 管理](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)中跟踪策略表示式的演示。 快进到 21:00 查看演示。

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png







<!--HONumber=Jan17_HO5-->


