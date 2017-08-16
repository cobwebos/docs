---
title: "配置 Web 应用程序防火墙 - Azure 应用程序网关 | Microsoft Docs"
description: "本文提供有关如何在现有的或新的应用程序网关上开始使用 Web 应用程序防火墙的指南。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ac6c629ceaf1a8036643f593ce3d7ef9ea096ef8
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>使用 Azure CLI 在新的或现有的应用程序网关上配置 Web 应用程序防火墙

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何创建启用了 Web 应用程序防火墙的应用程序网关或如何将 Web 应用程序防火墙添加到现有的应用程序网关。

Azure 应用程序网关中的 Web 应用程序防火墙 (WAF) 可保护 Web 应用程序，使其免受常见 Web 攻击的威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。

Azure 应用程序网关是第 7 层负载均衡器。 它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。 应用程序网关提供许多应用程序传送控制器 (ADC) 功能，包括 HTTP 负载均衡、基于 Cookie 的会话相关性、安全套接字层 (SSL) 卸载、自定义运行状况探测、多站点支持，以及许多其他功能。 若要查找支持的功能的完整列表，请参阅[应用程序网关概述](application-gateway-introduction.md)。

以下文章说明如何[将 Web 应用程序防火墙添加到现有的应用程序网关](#add-web-application-firewall-to-an-existing-application-gateway)以及如何[创建使用 Web 应用程序防火墙的应用程序网关](#create-an-application-gateway-with-web-application-firewall)。

![方案图像][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>先决条件：安装 Azure CLI 2.0

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。

## <a name="waf-configuration-differences"></a>WAF 配置差异

如果已阅读[使用 Azure CLI 创建应用程序网关](application-gateway-create-gateway-cli.md)，就能了解创建应用程序网关时需要配置的 SKU 设置。 在应用程序网关上配置 SKU 时，WAF 会提供其他可定义的设置。 不需要对应用程序网关本身进行任何其他更改。

| **设置** | **详细信息**
|---|---|
|**SKU** |没有 WAF 的普通应用程序网关支持 **Standard\_Small**、**Standard\_Medium** 和 **Standard\_Large** 大小。 随着 WAF 的引入，还提供了其他两个 SKU，分别是 **WAF\_Medium** 和 **WAF\_Large**。 小型应用程序网关不支持 WAF。|
|**模式** | 此设置是 WAF 的模式。 允许的值为**检测**和**阻止**。 WAF 设置为检测模式时，所有威胁都会存储在日志文件中。 在阻止模式下，仍然会记录事件，但攻击者会从应用程序网关收到“403 未授权”响应。|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>将 Web 应用程序防火墙添加到现有的应用程序网关

以下命令会将现有的标准应用程序网关更改为启用 WAF 的应用程序网关。

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

此命令使用 Web 应用程序防火墙更新应用程序网关。 请查看[应用程序网关诊断](application-gateway-diagnostics.md)，了解如何查看应用程序网关的日志。 由于 WAF 的安全特性，需要定期查看日志，以了解 Web 应用程序的安全状态。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>创建具有 Web 应用程序防火墙的应用程序网关

以下命令将创建具有 Web 应用程序防火墙的应用程序网关。

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> 为使用基本 Web 应用程序防火墙配置创建的应用程序网关配置 CRS 3.0 以进行保护。

## <a name="get-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不方便。 若要确保最终用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 [在 Azure 中配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 若要配置 CNAME 记录，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关和与之关联的 IP/DNS 名称的详细信息。 应使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

查看[通过 Azure CLI 2.0 自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-cli.md)，了解如何自定义 WAF 规则。

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png

