---
title: 如何使用健康状况文本分析
titleSuffix: Azure Cognitive Services
description: 了解如何健康状况文本分析从非结构化临床文本中提取和标记医疗信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: dffd12f319bd2766decda5874299cd7115f0502b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309194"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>如何：使用健康状况文本分析（预览）

> [!NOTE]
> 运行状况容器的文本分析最近已更新。 有关最近更改的详细信息，请参阅 [新增功能](../whats-new.md) 。 请记得提取最新的容器以使用列出的更新。

> [!IMPORTANT] 
> 健康状况文本分析是一项预览功能，其“按原样”提供并在“不保证没有缺点”情况下提供。 因此，不应在任何生产用途中实施或部署健康状况文本分析（预览版）。 健康状况文本分析不应用于或不可供用于医疗设备、临床支持、诊断工具或者其他旨在用于诊断、治愈、缓解、治疗或预防疾病或其他健康问题的技术，Microsoft 不授予将此功能用于此类目的的任何许可或权利。 此功能不旨在代替专业人员医疗建议或保健意见、诊断、治疗或医疗保健专业人员临床判断而实施或部署，并且不应用作此用途。 客户独自负责健康状况文本分析的任何使用。 Microsoft 不保证健康状况文本分析或提供的与该功能相关的任何材料足够充分用于任何医疗目的，或者满足任何人的健康或医疗要求。 


健康状况文本分析是一种容器化服务，它从非结构化文本（如医生的备注、出院摘要、临床文档和电子健康状况记录）中提取和标记相关医疗信息。  

## <a name="features"></a>功能

健康状况文本分析容器当前在满足特定安全和数据管理要求的用户自身开发环境中执行英语文本的命名实体识别 (NER)、关系提取、实体否定和实体链接。

#### <a name="named-entity-recognition"></a>[命名实体识别](#tab/ner)

命名实体识别检测非结构化文本中提及的可与一个或多个语义类型关联的字词和短语，如诊断、药物名称、症状/体征或年龄。

> [!div class="mx-imgBorder"]
> ![健康状况 NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[关系提取](#tab/relation-extraction)

关系提取标识文本中提及的概念之间有意义的联系。 例如，通过将疾病名称与时间关联来查找“疾病时间”关系。 

> [!div class="mx-imgBorder"]
> ![健康状况 RE](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[实体链接](#tab/entity-linking)

实体链接将文本中提及的命名实体与预定义概念数据库中找到的概念相关联来消除不同实体的歧义。 例如，统一医学语言系统 (UMLS)。

> [!div class="mx-imgBorder"]
> ![健康状况 EL](../media/ta-for-health/health-entity-linking.png)

健康状况文本分析支持链接到统一医学语言系统 ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) 元词表知识源中的健康状况和生物医学词汇。

#### <a name="negation-detection"></a>[否定检测](#tab/negation-detection) 

医学内容的意义受到修饰语（如否定）的高度影响，这在误诊时可能有重大影响。 健康状况文本分析支持对文本中提到的不同实体进行否定检测。 

> [!div class="mx-imgBorder"]
> ![健康状况 NEG](../media/ta-for-health/health-negation.png)

---

有关支持的实体的完整列表，请参阅由文本分析返回的 [实体类别](../named-entity-types.md?tabs=health) 。

## <a name="supported-languages"></a>支持的语言

健康状况文本分析仅支持英语文档。

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[认知服务容器请求表单](https://aka.ms/csgate)以请求访问容器。 目前，将不会对健康状况文本分析容器的使收费。 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>安装容器

可以通过多种方式来安装和运行容器。 

- 使用 [Azure 门户](text-analytics-how-to-install-containers.md?tabs=healthcare)创建文本分析资源，并使用 Docker 获取容器。
- 使用以下 PowerShell 和 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 脚本来自动执行资源部署容器配置。

### <a name="install-the-container-using-azure-web-app-for-containers"></a>使用 Azure 用于容器的 Web 应用安装容器

Azure [用于容器的 Web 应用](https://azure.microsoft.com/services/app-service/containers/)是专用于在云中运行容器的 Azure 资源。 它提供自动缩放、Docker 容器和 Docker 编写支持、HTTPS 支持等开箱即用功能。

> [!NOTE]
> 使用 Azure Web 应用时，将自动获取 `<appservice_name>.azurewebsites.net` 格式的域

通过 HTTPS 使用订阅和容器映像，从而利用 Azure CLI 运行此 PowerShell 脚本来创建用于容器的 Web 应用。 等待脚本完成 (大约25-30 分钟) ，然后提交第一个请求。

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>使用 Azure 容器实例安装容器

还可以使用 Azure 容器实例 (ACI) 更轻松地部署。 ACI 资源允许在托管的无服务器 Azure 环境中按需运行 Docker 容器。 

有关使用 Azure 门户部署 ACI 资源的步骤，请参阅[如何使用 Azure 容器实例](text-analytics-how-to-use-container-instances.md)。 还可以通过 Azure CLI 使用以下 PowerShell 脚本，这将使用容器映像在订阅上创建 ACI。  等待脚本完成 (大约25-30 分钟) ，然后提交第一个请求。  由于每个 ACI 资源的最大 Cpu 数的限制，如果你希望提交超过5个大文档 (每个请求) 约5000个字符，请不要选择此选项。
有关可用性信息，请参阅 [ACI 区域支持](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) 文章。 

> [!NOTE] 
> Azure 容器实例不包括对内置域的 HTTPS 支持。 如果需要 HTTPS，则需要手动配置它，包括创建证书和注册域。 可以通过下面的 NGINX 查找有关如何执行此操作的说明。

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>保护 ACI 连接

默认情况下，将 ACI 与容器 API 一起使用时不提供安全性。 这是因为大多数情况下容器会作为 Pod 的一部分运行，而 Pod 受网络桥的保护，与外部隔离。 但是，可以使用前端组件来修改容器，使容器终结点保持专用。 以下示例使用 [NGINX](https://www.nginx.com) 作为入口网关，以支持 HTTPS/SSL 和客户端证书身份验证。

> [!NOTE]
> NGINX 是高性能的开源 HTTP 服务器和代理。 NGINX 容器可用于终止单个容器的 TLS 连接。 还可能会有更复杂的基于 NGINX 入口的 TLS 终止解决方案。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>将 NGINX 设置为入口网关

NGINX 使用[配置文件](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)在运行时启用功能。 若要为另一个服务启用 TLS 终止，必须指定用于终止 TLS 连接的 SSL 证书，以及用于指定服务地址的 `proxy_pass`。 下面提供了示例。


> [!NOTE]
> `ssl_certificate` 需要路径在 NGINX 容器的本地文件系统中进行指定。 为 `proxy_pass` 指定的地址必须在 NGINX 容器的网络中可用。

NGINX 容器将 `/etc/nginx/conf.d/` 下装载的 `_.conf_` 中的所有文件加载到 HTTP 配置路径。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>示例 Docker compose 文件

下面的示例演示如何创建 [docker compose](https://docs.docker.com/compose/reference/overview) 文件来部署 NGINX 和健康状况文本分析容器：

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

若要启动此 Docker compose 文件，请在文件根级别从控制台中执行以下命令：

```bash
docker-compose up
```

有关详细信息，请参阅有关 [NGINX SSL 终止](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)的 NGINX 文档。


## <a name="example-api-request"></a>示例 API 请求
该容器提供基于 REST 的查询预测终结点 API。

使用下面的示例 cURL 请求将查询提交到已部署的容器，并使用适当的值替换 `serverURL` 变量。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

以下 JSON 是附加到健康状况文本分析 API 请求 POST 正文的 JSON 文件示例：

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>API 响应正文

以下 JSON 是健康状况文本分析 API 响应正文的示例：

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>求反检测输出

使用求反检测时，在某些情况下，单个否定字词可以同时处理多个字词。 通过 `isNegated` 标志的布尔值将已识别实体的否定表示在 JSON 输出中：

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>关系提取输出

关系提取输出包含对关系 *源* 及其 *目标*的 URI 引用。 具有关系角色的实体 `ENTITY` 分配给 `target` 字段。 具有关系角色的实体 `ATTRIBUTE` 分配给 `source` 字段。 缩写关系包含双向 `source` 和 `target` 字段，并且 `bidirectional` 将设置为 `true` 。 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [命名实体类别](../named-entity-types.md)
* [新增功能](../whats-new.md)
