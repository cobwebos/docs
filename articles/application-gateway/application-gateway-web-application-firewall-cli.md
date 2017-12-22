---
title: "配置 Web 应用程序防火墙：Azure 应用程序网关 | Microsoft 文档"
description: "本文将简要介绍如何在现有的或新的应用程序网关上开始使用 Web 应用程序防火墙。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: e60bfc89378569b154f4f973d1dceb683fa58482
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>使用 Azure CLI 在新的或现有应用程序网关上配置 Web 应用程序防火墙

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何创建启用 Web 应用程序防火墙 (WAF) 的应用程序网关。 此外，了解如何将 WAF 添加到现有的应用程序网关。

Azure 应用程序网关中的 WAF 可保护 Web 应用程序，使其免受常见的基于 Web 的攻击威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。

 应用程序网关是第 7 层负载均衡器。 它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。 应用程序网关提供了许多应用程序传送控制器 (ADC) 功能：

 * HTTP 负载均衡 
 * 基于 Cookie 的会话关联 
 * 安全套接字层 (SSL) 卸载 
 * 自定义运行状况探测 
 * 对多站点功能的支持
 
 若要查找支持功能的完整列表，请参阅[应用程序网关概述](application-gateway-introduction.md)。

本文介绍如何[将 Web 应用程序防火墙添加到现有应用程序网关](#add-web-application-firewall-to-an-existing-application-gateway)。 此外，它还演示如何[创建使用 Web 应用程序防火墙的应用程序网关](#create-an-application-gateway-with-web-application-firewall)。

![方案图像][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>先决条件：安装 Azure CLI 2.0

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2)。

## <a name="waf-configuration-differences"></a>WAF 配置差异

如果已阅读[使用 Azure CLI 创建应用程序网关](application-gateway-create-gateway-cli.md)，就能了解创建应用程序网关时需要配置的 SKU 设置。 在应用程序网关上配置 SKU 时，WAF 会提供其他可定义的设置。 不需要对应用程序网关本身进行任何其他更改。

| **设置** | **详细信息**
|---|---|
|**SKU** |没有 WAF 的普通应用程序网关支持 Standard\_Small、Standard\_Medium 和 Standard\_Large 大小。 随着 WAF 的引入，还提供了其他两个 SKU，分别是 WAF\_Medium 和 WAF\_Large。 小型应用程序网关不支持 WAF。|
|**模式** | 此设置为 WAF 模式。 允许的值为“检测”和“阻止”。 WAF 设置为“检测”模式时，所有威胁都会存储在日志文件中。 在“阻止”模式下，仍然会记录事件，但攻击者会从应用程序网关收到“403 未授权”响应。|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>将 Web 应用程序防火墙添加到现有的应用程序网关

以下命令会将现有的标准应用程序网关更改为启用 WAF 的应用程序网关：

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

此命令使用 WAF 更新应用程序网关。 了解如何查看应用程序网关的日志，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。 由于 WAF 的安全特性，请定期查看日志，以了解 Web 应用程序的安全状态。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>创建具有 Web 应用程序防火墙的应用程序网关

以下命令将创建具有 WAF 的应用程序网关：

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
> 为使用基本 WAF 配置创建的应用程序网关配置 CRS 3.0 以进行保护。

## <a name="get-an-application-gateway-dns-name"></a>获取应用程序网关 DNS 名称

创建网关后，下一步是配置前端以进行通信。 使用公共 IP 时，应用程序网关需要动态分配的 DNS 名称，这会造成不便。 若要确保用户能够访问应用程序网关，可以使用 CNAME 记录指向应用程序网关的公共终结点。 有关详细信息，请参阅[配置 Azure 云服务的自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。 

若要配置 CNAME 记录，可使用附加到应用程序网关的 PublicIPAddress 元素检索应用程序网关和与之关联的 IP/DNS 名称的详细信息。 使用应用程序网关的 DNS 名称来创建 CNAME 记录，使两个 Web 应用程序都指向此 DNS 名称。 不建议使用 A 记录，因为在重新启动应用程序网关时可能会更改 VIP。

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

若要了解如何自定义 WAF 规则，请参阅[通过 Azure CLI 2.0 自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-cli.md)。

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
