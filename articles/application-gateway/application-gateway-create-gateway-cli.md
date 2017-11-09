---
title: "创建应用程序网关 - Azure CLI 2.0 | Microsoft Docs"
description: "了解如何在资源管理器中使用 Azure CLI 2.0 创建应用程序网关。"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 3f43feaa8866725c5e6d8aa8cb03cd1a945de453
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 经典 PowerShell](application-gateway-create-gateway.md)
> * [Azure 资源管理器模板](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Azure 应用程序网关是专用虚拟设备，以服务形式提供应用程序传送控制器 (ADC)，为应用程序提供各种第 7 层负载均衡功能。

## <a name="cli-versions"></a>CLI 版本

可以使用下面一种版本的命令行接口 (CLI) 创建应用程序网关：

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md)：适用于经典部署模型和 Azure 资源管理器部署模型的 Azure CLI
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md)：适用于资源管理器部署模型的新一代 CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>先决条件：安装 Azure CLI 2.0

若要执行本文中的步骤，需要[安装适用于 macOS、Linux 和 Windows 的 Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

> [!NOTE]
> 必须有 Azure 帐户，才能创建应用程序网关。 如果没有订阅，可以注册[免费试用版](../active-directory/sign-up-organization.md)。

## <a name="scenario"></a>方案

在此方案中，将了解如何使用 Azure 门户创建应用程序网关。

此方案将：

* 创建包含两个实例的中型应用程序网关。
* 创建名为 AdatumAppGatewayVNET 且包含 10.0.0.0/16 保留 CIDR 块的虚拟网络。
* 创建名为 Appgatewaysubnet 且使用 10.0.0.0/28 作为其 CIDR 块的子网。

> [!NOTE]
> 在应用程序网关创建后（而不是在初始部署期间），进一步配置应用程序网关，包括自定义运行状况探测、后端池地址和其他规则。

## <a name="before-you-begin"></a>开始之前

应用程序网关需要自己的子网。 创建虚拟网络时，请务必为多个子网留出足够的地址空间。 将应用程序网关部署到子网后，只能向此子网添加其他应用程序网关。

## <a name="sign-in-to-azure"></a>登录 Azure

打开 Microsoft Azure 命令提示符，并登录：

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> 还可以使用不带开关的 `az login` 进行设备登录，登录时需要在 aka.ms/devicelogin 输入代码。

输入上一命令后，便会收到代码。 在浏览器中，转到 https://aka.ms/devicelogin，继续进行登录。

![显示设备登录信息的命令提示符][1]

在浏览器中，输入收到的代码。 这样，可以重定向到登录页。

![用于输入代码的浏览器][2]

输入代码进行登录，再关闭浏览器，以便继续操作。

![已成功登录][3]

## <a name="create-the-resource-group"></a>创建资源组

创建应用程序网关前，先创建一个用于容纳它的资源组。 请使用以下命令：

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

对后端服务器 IP 地址使用后端 IP 地址。 这些值可以是虚拟网络中的专用 IP、公共 IP 或后端服务器的完全限定域名。 下面的示例创建应用程序网关，针对 HTTP 设置、端口和规则进行了其他配置：

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

上一示例展示了应用程序网关创建期间的可选属性。 以下代码示例使用必需属性创建应用程序网关：

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> 有关要在创建期间使用的参数列表，请运行以下命令：`az network application-gateway create --help`。

此示例使用侦听器、后端池、后端 HTTP 设置和规则的默认设置，创建基本应用程序网关。 预配成功后，即可根据部署需求修改这些设置。

如果 Web 应用程序是通过前面步骤中的后端池进行定义，负载均衡便会立即生效。

## <a name="get-the-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称
创建网关后，需要配置用于通信的前端。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，但这并不易用。 为了确保用户能够访问应用程序网关，可以使用 CNAME 记录，指向应用程序网关的公共终结点。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](../dns/dns-custom-domain.md)。

若要配置别名，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 请勿使用 A 记录，因为重启应用程序网关后 VIP 可能会发生变化。


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请运行以下命令：

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>后续步骤

若要了解如何创建自定义运行状况探测，请转到[使用门户创建应用程序网关的自定义探测](application-gateway-create-probe-portal.md)。

若要了解如何配置 SSL 卸载，并从 Web 服务器中移除成本高昂的 SSL 解密，请参阅[使用 Azure 资源管理器配置应用程序网关以进行 SSL 卸载](application-gateway-ssl-arm.md)。

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
