---
title: 在 Azure 中使用 Terraform 创建中心虚拟网络
description: 本教程将介绍如何在 Azure 中创建可充当其他网络之间的常用连接点的中心 VNet
services: terraform
ms.service: azure
keywords: terraform, 中心辐射型, 网络, 混合网络, devops, 虚拟机, azure, VNet 对等互连, 中心辐射型, 中心。
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 5f8d9119ea9caeb598946a384f321fa6f7e14def
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000217"
---
# <a name="tutorial-create-a-hub-virtual-network-with-terraform-in-azure"></a>教程：在 Azure 中使用 Terraform 创建中心虚拟网络

中心虚拟网络 (VNet) 充当到本地网络的连接的中心点。 VNet 承载由辐射型 VNet 中托管的工作负载占用的共享服务。 出于演示目的，本教程中不实施共享服务。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）在中心辐射型拓扑中实现中心 VNet
> * 使用 Terraform 创建中心跳转盒虚拟机
> * 使用 Terraform 创建中心虚拟专用网络网关
> * 使用 Terraform 创建中心和本地网关连接

## <a name="prerequisites"></a>先决条件

1. [在 Azure 中使用 Terraform 创建中心辐射型混合网络拓扑](./terraform-hub-spoke-introduction.md)。
1. [在 Azure 中使用Terraform 创建本地虚拟网络](./terraform-hub-spoke-on-prem.md)。

## <a name="create-the-directory-structure"></a>创建目录结构

中心网络由以下部分组成：

- 中心虚拟网络
- 中心虚拟网络网关
- 中心网关连接 

以下 Terraform 配置文件进行了资源定义：

1. 浏览到 [Azure 门户](https://portal.azure.com)。

1. 打开 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果事先未选择环境，请选择“Bash”作为环境。

    ![Cloud Shell 提示符](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. 切换到 `clouddrive` 目录。

    ```bash
    cd clouddrive
    ```

1. 将目录切换到新目录：

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-vnet"></a>声明中心 VNet

创建会声明中心虚拟网络的 Terraform 配置文件。

1. 在 Cloud Shell 中，创建名为 `hub-vnet.tf` 的文件。

    ```bash
    code hub-vnet.tf
    ```

1. 在编辑器中粘贴以下代码：

    ```JSON
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = "${local.hub-resource-group}"
      location = "${local.hub-location}"
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub}"
        subnet_id                     = "${azurerm_subnet.hub-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = "${local.prefix-hub}"
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub}"
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.hub-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.hub-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.hub-vnet-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"

      shared_key = "${local.shared-key}"
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.hub-vnet-gateway.id}"

      shared_key = "${local.shared-key}"
    }
    ```
    
1. 保存文件并退出编辑器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [在 Azure 中使用 Terraform 创建中心虚拟网络设备](./terraform-hub-spoke-hub-nva.md)）