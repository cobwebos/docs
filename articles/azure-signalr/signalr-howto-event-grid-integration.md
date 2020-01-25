---
title: 如何将 Azure SignalR 服务事件发送到事件网格
description: 本指南介绍如何为 SignalR 服务启用事件网格事件，然后将客户端连接连接/断开连接的事件发送到示例应用程序。
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710833"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>如何将事件从 Azure SignalR Service 发送到事件网格

Azure 事件网格是一种完全托管的事件路由服务，它使用 pub 子模型提供统一的事件消耗。 在本指南中，将使用 Azure CLI 创建 Azure SignalR 服务、订阅连接事件，并部署一个示例 web 应用程序来接收事件。 最后，你可以连接和断开连接，并在示例应用程序中查看事件负载。

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][azure-account]을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 문서에서 Azure CLI 명령은 **Bash** 셸에서 실행하도록 형식이 지정됩니다. PowerShell 또는 명령 프롬프트와 같은 다른 셸을 사용하는 경우 줄 연속 문자 또는 변수 할당 줄을 적절히 조정해야 합니다. 이 문서에서는 변수를 사용하여 필요한 명령 편집 작업을 최소화합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리 컨테이너입니다. 以下[az group create][az-group-create]命令会在*eastus*区域中创建名为*myResourceGroup*的资源组。 리소스 그룹에 다른 이름을 사용하려면 `RESOURCE_GROUP_NAME`을 다른 값으로 설정합니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR Service 만들기

接下来，使用以下命令将 Azure Signalr 服务部署到资源组。
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

创建 SignalR 服务后，Azure CLI 将返回类似于下面的输出：

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>이벤트 엔드포인트 만들기

이 섹션에서는 GitHub 리포지토리에 있는 Resource Manager 템플릿을 사용하여 Azure App Service에 미리 작성된 샘플 웹 애플리케이션을 배포합니다. 나중에 레지스트리의 Event Grid 이벤트를 구독하고 이벤트가 전송되는 엔드포인트로 이 앱을 지정합니다.

샘플 앱을 배포하려면 `SITE_NAME`을 웹앱의 고유한 이름으로 설정하고 다음 명령을 실행합니다. 사이트 이름은 웹앱의 FQDN(정규화된 도메인 이름)의 일부를 형성하기 때문에 Azure 내에서 고유해야 합니다. 이후 섹션에서 레지스트리의 이벤트를 보려면 웹 브라우저에서 앱의 FQDN으로 이동합니다.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

部署成功后（可能需要几分钟的时间），打开浏览器并导航到 web 应用，确保其正在运行：

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>레지스트리 이벤트 구독

Event Grid에서 *항목*을 구독하여 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다. 以下[az eventgrid event-订阅 create][az-eventgrid-event-subscription-create]命令订阅你创建的 Azure SignalR 服务，并将你的 web 应用的 URL 指定为它应将事件发送到的终结点。 이전 섹션에서 채워진 환경 변수가 여기에 재사용되므로 편집이 필요 없습니다.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

구독이 완료되면 다음과 비슷한 출력이 표시되어야 합니다.

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>레지스트리 이벤트 트리거

切换到服务模式以 `Serverless Mode` 并设置与 SignalR 服务建立的客户端连接。 您可以采用[无服务器示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)作为参考。

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>레지스트리 이벤트 보기

现已将客户端连接到 SignalR 服务。 导航到事件网格查看器 web 应用，应会看到 `ClientConnectionConnected` 事件。 如果终止该客户端，则还会看到 `ClientConnectionDisconnected` 事件。

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
