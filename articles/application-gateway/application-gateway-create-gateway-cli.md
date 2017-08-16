---
title: "创建 Azure 应用程序网关 - Azure CLI 2.0 | Microsoft 文档"
description: "了解如何在 Resource Manager 中使用 Azure CLI 2.0 创建应用程序网关"
services: application-gateway
documentationcenter: na
author: georgewallace
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
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 052410db8c7619c7990dc319951a55663f2c2ba1
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 经典 PowerShell](application-gateway-create-gateway.md)
> * [Azure 资源管理器模板](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

应用程序网关是一个专用的虚拟设备，以服务形式提供应用程序传送控制器 (ADC)，为应用程序提供各种第 7 层负载均衡功能。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI。
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md) - 适用于资源管理部署模型的下一代 CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>先决条件：安装 Azure CLI 2.0

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

> [!NOTE]
> 如果没有 Azure 帐户，则需要注册一个。 可以[在此处注册免费试用帐户](../active-directory/sign-up-organization.md)。

## <a name="scenario"></a>方案

在此方案中，将学习如何使用 Azure 门户创建应用程序网关。

此方案将：

* 创建包含两个实例的中型应用程序网关。
* 创建名为 AdatumAppGatewayVNET 且包含 10.0.0.0/16 保留 CIDR 块的虚拟网络。
* 创建名为 Appgatewaysubnet 且使用 10.0.0.0/28 作为其 CIDR 块的子网。

> [!NOTE]
> 针对应用程序网关进行的其他配置（包括自定义运行状况探测、后端池地址以及其他规则）是在对应用程序网关配置以后配置的，不是在初始部署期间配置的。

## <a name="before-you-begin"></a>开始之前

Azure 应用程序网关需要自己的子网。 在创建虚拟网络时，请确保保留足够的地址空间，以便设置多个子网。 将应用程序网关部署到子网后，只能向该子网添加其他应用程序网关。

## <a name="log-in-to-azure"></a>登录 Azure

打开 **Microsoft Azure 命令提示符**，并登录。 

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> 还可以使用不带开关的 `az login` 进行设备登录，登录时需要在 aka.ms/devicelogin 输入代码。

键入前述示例后，将提供代码。 在浏览器中导航到 https://aka.ms/devicelogin，继续登录过程。

![显示设备登录信息的 cmd][1]

在浏览器中，输入收到的代码。 将重定向至登录页。

![用于输入代码的浏览器][2]

输入代码后即已登录，关闭浏览器以继续完成方案。

![已成功登录][3]

## <a name="create-the-resource-group"></a>创建资源组

在创建应用程序网关前，会创建资源组以包含应用程序网关。 以下显示该命令。

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

用于后端的 IP 地址是后端服务器的 IP 地址。 这些值可以是虚拟网络中的专用 IP、公共 IP 或后端服务器的完全限定域名。 以下示例使用用于 http 设置、端口和规则的其他配置设置创建应用程序网关。

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

前面的示例展示创建应用程序网关期间不需要的许多属性。 以下代码示例创建含所需信息的应用程序网关。

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
> 如需在创建过程中能够提供的参数的列表，请运行以下命令：`az network application-gateway create --help`。

此示例会创建基本的应用程序网关，提供的默认设置适用于侦听器、后端池、后端 http 设置以及规则。 预配成功后，即可根据部署修改这些设置。
如果在之前的步骤中已使用后端池定义 Web 应用程序，则在创建后，负载均衡即会开始。

## <a name="get-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不方便。 若要确保最终用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 [在 Azure 中配置自定义域名](../dns/dns-custom-domain.md)。 若要配置别名，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 应使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。


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

若要删除在本文中创建的所有资源，请完成以下步骤：

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>后续步骤

访问[创建自定义运行状况探测](application-gateway-create-probe-portal.md)，了解如何创建自定义运行状况探测

访问[配置 SSL 卸载](application-gateway-ssl-arm.md)，了解如何配置 SSL 卸载并从 Web 服务器中剥离开销较高的 SSL 解密

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

