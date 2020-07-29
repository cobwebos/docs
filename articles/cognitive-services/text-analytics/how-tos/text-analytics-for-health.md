---
title: 如何将文本分析用于运行状况
titleSuffix: Azure Cognitive Services
description: 了解如何从非结构化临床文本中提取和标记医疗信息，并提供文本分析的运行状况。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: dbd0699924268b38d69bc576a5886e8d31fa1208
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373464"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>如何：将文本分析用于运行状况（预览版）

> [!IMPORTANT] 
> 针对运行状况的文本分析是 "按原样" 和 "所有错误" 提供的预览功能。 因此，**不应在任何生产用途中实现或部署文本分析的运行状况（预览版）。** 针对运行状况的文本分析不是要用作医疗设备、临床支持、诊断工具或其他旨在用于诊断、硬化、缓解、治疗或防范疾病或其他情况的技术，Microsoft 不会授予使用此功能的任何许可或权利。 此功能不是专为实现或部署的，而是作为一种替代专业人员的医疗建议或医疗保健、诊断、治疗或医疗保健专业人员的临床判断，不应如此。 客户独自负责使用文本分析的健康状况。 Microsoft 不保证文本分析的运行状况或与功能连接的任何材料都足以满足任何医疗目的或满足任何人的健康或医疗要求。 


针对运行状况的文本分析是一种容器化的服务，它从非结构化文本（如医生的说明、解雇摘要、临床文档和电子运行状况记录）提取和标记相关的医疗信息。  

## <a name="features"></a>功能

运行状况容器文本分析当前在你自己的开发环境中针对英语文本执行命名实体识别（NER）、关系提取、实体求反和实体链接，以满足你的特定安全和数据管理要求。

#### <a name="named-entity-recognition"></a>[命名实体识别](#tab/ner)

命名实体识别可检测可与一个或多个语义类型关联的非结构化文本中提到的字词和短语，如诊断、药物名称、征兆/符号或年龄。

> [!div class="mx-imgBorder"]
> ![运行状况 NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[关系提取](#tab/relation-extraction)

关系提取标识文本中所述概念之间的有意义的连接。 例如，通过将条件名称与时间关联来查找 "条件时间" 关系。 

> [!div class="mx-imgBorder"]
> ![运行状况重新](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[实体链接](#tab/entity-linking)

实体链接消除不同的实体，方法是将文本中提到的命名实体关联到在概念的预定义数据库中找到的概念。 例如，统一的医疗语言系统（UMLS）。

> [!div class="mx-imgBorder"]
> ![运行状况 EL](../media/ta-for-health/health-entity-linking.png)

用于运行状况的文本分析支持链接到在统一的医疗语言系统（[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)） Metathesaurus 知识源中找到的运行状况和生物医学词汇。

#### <a name="negation-detection"></a>[求反检测](#tab/negation-detection) 

医疗内容的意义由修饰符（如求反）高度影响，这在误诊时可能会有重要含义。 针对运行状况的文本分析支持对文本中提到的不同实体进行求反检测。 

> [!div class="mx-imgBorder"]
> ![运行状况 NEG](../media/ta-for-health/health-negation.png)

---

有关支持的实体的完整列表，请参阅由文本分析返回的[实体类别](../named-entity-types.md?tabs=health)。

## <a name="supported-languages"></a>支持的语言

运行状况文本分析仅支持英语文档。

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[认知服务容器请求窗体](https://aka.ms/cognitivegate)，请求对容器的访问权限。 目前，不会向你收取文本分析的健康状况。 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>安装容器

可以通过多种方式来安装和运行容器。 

- 使用[Azure 门户](text-analytics-how-to-install-containers.md?tabs=healthcare)创建文本分析资源，并使用 Docker 获取容器。
- 使用以下 PowerShell 和[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)脚本来自动执行资源部署容器配置。

### <a name="install-the-container-using-azure-web-app-for-containers"></a>使用 Azure 用于容器的 Web 应用安装容器

Azure[用于容器的 Web 应用](https://azure.microsoft.com/services/app-service/containers/)是专用于在云中运行容器的 azure 资源。 它提供了现成的功能，如自动缩放、对 docker 容器的支持、docker 编写、HTTPS 支持等等。

> [!NOTE]
> 使用 Azure Web 应用时，将自动获取`<appservice_name>.azurewebsites.net`

使用 Azure CLI 运行此 PowerShell 脚本，使用你的订阅和 HTTPS 上的容器映像创建用于容器的 Web 应用。 等待脚本完成（大约20分钟），然后提交第一个请求。

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

还可以使用 Azure 容器实例（ACI）简化部署。 ACI 是一项资源，允许在托管的无服务器 Azure 环境中按需运行 Docker 容器。 

有关使用 Azure 门户部署 ACI 资源的步骤，请参阅[如何使用 Azure 容器实例](text-analytics-how-to-use-container-instances.md)。 还可以使用以下 PowerShell 脚本 Azure CLI，这将使用容器映像在订阅上创建 ACI。  等待脚本完成（大约20分钟），然后提交第一个请求。

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
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 5 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>安全的 ACI 连接

默认情况下，将 ACI 与容器 API 一起使用时不提供安全性。 这是因为，通常容器将作为 pod 的一部分运行，而该 pod 由网桥保护。 但是，可以使用正面组件来修改容器，使容器终结点保持私有。 以下示例使用[NGINX](https://www.nginx.com)作为入口网关，以支持 HTTPS/SSL 和客户端证书身份验证。

> [!NOTE]
> NGINX 是一个开源、高性能 HTTP 服务器和代理。 NGINX 容器可用于终止单个容器的 TLS 连接。 还可能会有更复杂的基于入口的 TLS 终止解决方案 NGINX。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>将 NGINX 设置为入口网关

NGINX 使用[配置文件](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)在运行时启用功能。 若要为另一个服务启用 TLS 终止，必须指定一个 SSL 证书以终止 TLS 连接，并 `proxy_pass` 指定该服务的地址。 下面提供了一个示例。


> [!NOTE]
> `ssl_certificate`需要在 NGINX 容器的本地文件系统中指定一个路径。 为指定的地址 `proxy_pass` 必须在 NGINX 容器的网络中可用。

NGINX 容器会将中装入的中的所有文件都加载 `_.conf_` `/etc/nginx/conf.d/` 到 HTTP 配置路径中。

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

#### <a name="example-docker-compose-file"></a>Docker 撰写文件示例

下面的示例演示了如何创建[docker 合成](https://docs.docker.com/compose/reference/overview)文件来部署运行状况容器的 NGINX 和文本分析：

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

若要启动此 Docker 合成文件，请在文件根级别的控制台中执行以下命令：

```bash
docker-compose up
```

有关详细信息，请参阅 NGINX 的文档[NGINX SSL 终止](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)。


## <a name="example-api-request"></a>示例 API 请求
该容器提供基于 REST 的查询预测终结点 API。

使用下面的示例卷请求将查询提交到已部署的容器， `serverURL` 并将该变量替换为适当的值。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.0-preview.1/domains/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

以下 JSON 是附加到运行状况 API 请求的 POST 正文的文本分析的 JSON 文件的示例：

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

以下 JSON 是运行状况 API 响应正文的文本分析的示例：

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
                    "type": "SYMPTOM_OR_SIGN",
                    "score": 0.97,
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
                    "type": "DOSAGE",
                    "score": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "type": "MEDICATION_NAME",
                    "score": 0.99,
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
                    "type": "FREQUENCY",
                    "score": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DOSAGE_OF_MEDICATION",
                    "score": 1.0,
                    "entities": [
                        {
                            "id": "0",
                            "role": "ATTRIBUTE"
                        },
                        {
                            "id": "1",
                            "role": "ENTITY"
                        }
                    ]
                },
                {
                    "relationType": "FREQUENCY_OF_MEDICATION",
                    "score": 1.0,
                    "entities": [
                        {
                            "id": "1",
                            "role": "ENTITY"
                        },
                        {
                            "id": "2",
                            "role": "ATTRIBUTE"
                        }
                    ]
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-05-08"
}
```

> [!NOTE] 
> 对于求反检测，在某些情况下，单个求反字词一次可以处理多个字词。 在 JSON 输出中，通过标志的布尔值表示已识别的实体的求反结果 `isNegated` ：

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "type": "SYMPTOM_OR_SIGN",
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

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [命名实体类别](../named-entity-types.md)
* [新增功能](../whats-new.md)
