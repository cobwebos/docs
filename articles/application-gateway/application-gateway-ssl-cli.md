---
title: "配置 SSL 卸载 - Azure 应用程序网关 - Azure CLI 2.0 | Microsoft Docs"
description: "本文说明如何使用 Azure CLI 2.0 创建支持 SSL 卸载的应用程序网关"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 4bdca33dae2ce52fdeccdae9a67abb6667593f9d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 配置应用程序网关以进行 SSL 卸载

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-ssl-portal.md)
> * [Azure 资源管理器 PowerShell](application-gateway-ssl-arm.md)
> * [Azure 经典 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

可将 Azure 应用程序网关配置为在网关上终止安全套接字层 (SSL) 会话，以避免 Web 场中出现开销较高的 SSL 解密任务。 SSL 卸载也简化了前端服务器上的证书管理。

## <a name="prerequisite-install-the-azure-cli-20"></a>先决条件：安装 Azure CLI 2.0

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2)。

## <a name="required-components"></a>所需组件

* 后端服务器池：后端服务器的 IP 地址列表。 列出的 IP 地址应属于虚拟网络子网，或者是公共 IP/虚拟 IP 地址 (VIP)。
* 后端服务器池设置：每个池都有一些设置，例如端口、协议和基于 Cookie 的相关性。 这些设置绑定到池，并会应用到池中的所有服务器。
* 前端端口：此端口是应用程序网关上打开的公共端口。 流量将抵达此端口，并重定向到后端服务器之一。
* 侦听器：侦听器具有前端端口、协议（Http 或 Https；这些设置区分大小写）和 SSL 证书名称（如果要配置 SSL 卸载）。
* 规则：规则会绑定侦听器和后端服务器池，并定义当流量抵达特定侦听器时要定向到的后端服务器池。 目前仅支持 *基本* 规则。 *基本* 规则是一种轮循负载分发模式。

**其他配置说明**

对于 SSL 证书配置， **HttpListener** 中的协议应更改为 *Https* （区分大小写）。 需要将“SslCertificate”元素添加到“HttpListener”，其中包含针对 SSL 证书配置的变量值。 前端端口应更新为 443。

启用基于 Cookie 的相关性：可以配置应用程序网关，以确保来自客户端会话的请求始终定向到 Web 场中的同一 VM。 这种情况可通过插入允许网关适当定向流量的会话 Cookie 实现。 要启用基于 Cookie 的相关性，请在 **BackendHttpSettings** 元素中将 **CookieBasedAffinity** 设置为 *Enabled*。

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>在现有程序网关上配置 SSL 卸载

输入以下命令以在现有应用程序网关上配置 SSL 卸载：

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>创建支持 SSL 卸载的应用程序网关

以下示例创建支持 SSL 卸载的应用程序网关。 必须将证书和证书密码更新为有效私钥。

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。  使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不便。 若要确保最终用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 有关详细信息，请参阅[在 Azure 中配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要配置别名，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关及其关联的 IP/DNS 名称的详细信息。 使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。


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

## <a name="next-steps"></a>后续步骤

如果想要将应用程序网关配置为与内部负载均衡器配合使用，请参阅[创建具有内部负载均衡器 (ILB) 的应用程序网关](application-gateway-ilb.md)。

有关负载均衡选项的其他常规信息，请参阅：

* [Azure 负载均衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理器](https://azure.microsoft.com/documentation/services/traffic-manager/)
