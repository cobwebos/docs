---
title: Terraform：部署与 VNet 集成和专用终结点安全连接的前端 Web 应用和后端 Web 应用
description: 了解如何使用应用服务的 Terraform 提供程序部署两个与专用终结点和 VNet 集成安全连接的 Web 应用
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739827"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>创建两个与专用终结点和 VNet 集成安全连接的 Web 应用

本文演示使用[专用终结点](../networking/private-endpoint.md)和区域 [VNet 集成](../web-sites-integrate-with-vnet.md)，按照以下步骤安全地连接两个 Web 应用（前端和后端）的示例：
- 部署 VNet
- 为集成创建第一个子网
- 为专用终结点创建第二个子网，必须设置特定参数才能禁用网络策略
- 部署一个 PremiumV2 类型的应用服务计划或专用终结点功能所需的 PremiumV3
- 使用特定应用设置创建前端 Web 应用以使用专用 DNS 区域，[详细信息](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- 将前端 Web 应用连接到集成子网
- 创建后端 Web 应用
- 创建 DNS 专用区域，使用 Web 应用的专用链接区域的名称 privatelink.azurewebsites.net
- 将此区域链接到 VNet
- 在终结点子网中为后端 Web 应用创建专用终结点，并在之前创建的 DNS 专用区域中注册 DNS 名称（网站和 SCM）

## <a name="how-to-use-terraform-in-azure"></a>如何在 Azure 中使用 Terraform

浏览 [Azure 文档](/azure/developer/terraform/)以了解如何将 Terraform 与 Azure 结合使用。

## <a name="the-complete-terraform-file"></a>完整的 Terraform 文件

要使用此文件，必须更改 frontwebapp 和 backwebapp 资源的名称属性（Webapp 名称必须是全球唯一的 DNS 名称）。 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>后续步骤


> [详细了解如何在 Azure 中使用 Terraform](/azure/developer/terraform/)