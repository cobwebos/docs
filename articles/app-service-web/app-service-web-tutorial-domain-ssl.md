---
title: "向 Azure Web 应用中添加自定义域和 SSL | Microsoft Docs"
description: "了解如何通过添加你的公司品牌为投入生产准备 Azure Web 应用。 将自定义域名（虚域）映射到 Web 应用并使用自定义 SSL 证书保护其安全。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>向 Azure Web 应用中添加自定义域和 SSL

本教程展示了如何快速将自定义域名映射到 Azure Web 应用并使用自定义 SSL 证书保护其安全。 

## <a name="before-you-begin"></a>开始之前

在运行此示例之前，请在本地安装 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。

你还需要对你的相应域提供商的 DNS 配置页面具有管理访问权限。 例如，若要添加 `www.contoso.com`，你需要能够配置 `contoso.com` 的 DNS 条目。

最后，对于要上传和绑定的 SSL 证书，你需要具有一个有效的 .PFX 文件及其密码。 应当配置此 SSL 证书来保护你需要的自定义域名的安全。 在上面的示例中，你的 SSL 证书应当保护 `www.contoso.com` 的安全。 

## <a name="step-1---create-an-azure-web-app"></a>第 1 步 - 创建 Azure Web 应用

### <a name="log-in-to-azure"></a>登录 Azure

现在，我们将在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure 中托管该 Node.js 应用。  使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>创建资源组   
使用 [az group create](/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

若要查看适用于 `---location` 的可能值，请使用 `az appservice list-locations` Azure CLI 命令。

## <a name="create-an-app-service-plan"></a>创建应用服务计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建应用服务计划。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

以下示例创建一个名为 `myAppServicePlan` 且使用**基本**定价层的应用服务计划。

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息。 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>创建 Web 应用

现在已经创建了一个应用服务计划，请在 `myAppServicePlan` 应用服务计划中创建 Web 应用。 该 Web 应用提供托管空间来部署你的代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。 

在以下命令中，请将出现的 `<app_name>` 占位符替换为你自己的唯一应用名称。 该唯一名称将用作 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将任何自定义 DNS 条目映射到 Web 应用，然后向用户公开该条目。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息。 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

在 JSON 输出中，`defaultHostName` 显示了你的 Web 应用的默认域名。 在浏览器中，导航到此地址。

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>第 2 步 - 配置 DNS 映射

在此步骤中，你将添加一个从自定义域到你的 Web 应用默认域名 `<app_name>.azurewebsites.net` 的映射。 通常，你在域提供商的网站上执行此步骤。 每个域注册机构的网站都略有不同，因此你应当查阅你的提供商的文档。 但是，仍然有一些一般准则。 

### <a name="navigate-to-to-dns-management-page"></a>导航到 DNS 管理页面

首先，登录到你的域注册机构的网站。  

然后，找到用于管理 DNS 记录的页面。 查找站点中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。 通常可通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到连接。

在找到该页面后，查找允许添加或编辑 DNS 记录的链接。 这可能是**区域文件**或**DNS 记录**链接，也可能是**高级配置**链接。

### <a name="create-a-cname-record"></a>创建 CNAME 记录

添加一条 CNAME 记录，用以将所需的子域名称映射到你的 Web 应用的默认域名（`<app_name>.azurewebsites.net`，其中 `<app_name>` 是你的应用的唯一名称）。

对于 `www.contoso.com` 示例，你将创建一个将 `www` 主机名映射到 `<app_name>.azurewebsites.net` 的 CNAME。

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>第 3 步 - 在 Web 应用中配置自定义域

在域提供商的网站上完成主机名映射配置后，可以在 Web 应用中配置自定义域。 可以使用 [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) 命令添加此配置。 

在下面的命令中，请将 `<app_name>` 替换为你的唯一应用名称，将 <your_custom_domain> 替换为完全限定的自定义域名（例如 `www.contoso.com`）。 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

自定义域现在已完全映射到你的 Web 应用。 在浏览器中，导航到自定义域名。 例如：

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>第 4 步 - 将自定义 SSL 证书绑定到 Web 应用

现在你已有了一个 Azure Web 应用，你希望为其提供的域名显示在浏览器的地址栏中。 但是，如果现在导航到 `https://<your_custom_domain>`，则会发生证书错误。 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

之所以发生此错误是因为你的 Web 应用还没有与自定义域名匹配的 SSL 证书绑定。 但是，如果导航到 `https://<app_name>.azurewebsites.net`，则不会发生错误。 这是因为，默认情况下，你的应用以及所有 Azure 应用服务应用都由 `*.azurewebsites.net` 通配符域的 SSL 证书提供保护。 

为了通过自定义域名访问你的 Web 应用，你需要将自定义域的 SSL 证书绑定到该 Web 应用。 你将在此步骤中执行此操作。 

### <a name="upload-the-ssl-certificate"></a>上传 SSL 证书

使用 [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload) 命令将自定义域的 SSL 证书上传到你的 Web 应用。

在下面的命令中，请将 `<app_name>` 替换为你的唯一应用名称，将 `<path_to_ptx_file>` 替换为你的 .PFX 文件的路径，将 `<password>` 替换为你的证书的密码。 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

上传证书后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

在 JSON 输出中，`thumbprint` 显示了你上传的证书的指纹。 请复制其值供下一步骤使用。

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>将上传的 SSL 证书绑定到 Web 应用

你的 Web 应用现在具有了所需的自定义域名，并且它还具有一个用于保护该自定义域的 SSL 证书。 剩下的唯一要做的事情就是将已上传的证书绑定到 Web 应用。 你将使用 [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind) 命令执行此操作。

在下面的命令中，请将 `<app_name>` 替换为你的唯一应用名称，将 `<thumbprint-from-previous-output>` 替换为通过前一个命令获取的证书指纹。 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

将证书绑定到 Web 应用后，Azure CLI 将显示类似于以下示例的信息：

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

在浏览器中，导航到你的自定义域名的 HTTPS 终结点。 例如：

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>更多资源

[在 Azure 应用服务中购买和配置自定义域名](custom-dns-web-site-buydomains-web-app.md)
[为 Azure 应用服务购买和配置 SSL 证书](web-sites-purchase-ssl-web-site.md)

