---
title: 快速入门：使用 Azure Front Door 设置高可用性 - Azure CLI
description: 本快速入门将介绍如何通过 Azure CLI 使用 Azure Front Door 创建具有高可用性和高性能的全局 Web 应用程序。
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 6aa960837a3bfc7f8a04ca1f554fb10d635c2ea2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91347898"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 Front Door 以实现高度可用的全局 Web 应用程序

通过使用 Azure CLI 创建高度可用且高性能的全局 Web 应用程序，开始使用 Azure Front Door。

Front Door 会将 Web 流量定向到后端池中的特定资源。 你已定义前端域，请将资源添加到后端池，并创建路由规则。 本文使用一个后端池的简单配置，其中包含两个 Web 应用资源和一个使用默认路径匹配“/*”的路由规则。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure CLI 或 Azure Cloud Shell
- 确保将 front-door 扩展添加到 Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 可以使用现有资源组，也可以创建新组。

对于本快速入门，你需要两个资源组。 一个在“美国中部”，另一个在“美国中南部”。

使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true) 创建资源组：

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>创建 Web 应用的两个实例

本快速入门需要两个在不同的 Azure 区域中运行的 Web 应用程序实例。 这两个 Web 应用程序实例都以“主动/主动”模式运行，因此其中的任何一个实例都可以为流量提供服务。

如果还没有 Web 应用，请使用以下脚本设置两个示例 Web 应用。

### <a name="create-app-service-plans"></a>创建应用服务计划

在创建 Web 应用之前，你将需要两个应用服务计划，一个在“美国中部”，另一个在“美国中南部”。

使用 [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create&preserve-view=true) 创建应用服务计划：

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
-resource-groupg myRGFDSouthCentral
```

### <a name="create-web-apps"></a>创建 Web 应用

运行以下命令会在上一步的每个应用服务计划中创建一个 Web 应用。 Web 应用名称必须全局独一无二。

使用 [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az_webapp_create&preserve-view=true) 创建 Web 应用：

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

记下每个 Web 应用的默认主机名，以便在下一步部署 Front Door 时可以定义后端地址。

## <a name="create-the-front-door"></a>创建 Front Door

通过运行以下命令，使用默认的负载均衡设置、运行状况探测和路由规则创建一个基本的 Front Door：

使用 [az network front-door create](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext_front_door_az_network_front_door_create&preserve-view=true) 创建 Front Door：

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--resource-group：** 指定要在其中部署 Front Door 的资源组。

**--name：** 为你的 Azure Front Door 指定一个全局唯一的名称。 

**--accepted-protocols：** 接受的值为 **http** 和 **https**。 如果要使用两者，请同时指定它们，用空格进行分隔。

**--backend-address：** 在此处定义用空格分隔的两个 Web 应用主机名。

在部署成功完成后，记下 frontEndpoints 部分的主机名。

## <a name="test-the-front-door"></a>测试 Front Door

打开 Web 浏览器并输入从命令获取的主机名。 Front Door 会将你的请求定向到后端资源之一。

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door 测试页":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用 Front Door 创建的资源，请删除这两个资源组。 删除资源组时，也会删除 Front Door 及其所有相关资源。 

若要删除资源组，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true)：

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：
* Front Door
* 两个 Web 应用

若要了解如何将自定义域添加到 Front Door，请继续学习 Front Door 教程。

> [!div class="nextstepaction"]
> [添加自定义域](front-door-custom-domain.md)
