---
title: 教程 - 使用 Terraform 在 Azure 中创建辐射网络
description: 了解如何在中心-辐射拓扑中实现两个连接到某个中心的分支 VNet。
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: e717424330706a6a4ebcadc543cdb2cf9bebe774
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969354"
---
# <a name="tutorial-create-a-spoke-network-in-azure-using-terraform"></a>教程：使用 Terraform 在 Azure 中创建辐射网络

在本教程中，请实现两个独立的分支网络，以便演示如何分隔工作负荷。 这两个网络通过中心虚拟网络共享公用资源。 辐射可以用来隔离其自己的 VNet 中的工作负荷，独立于其他辐射进行管理。 每个工作负荷可以包括多个层，并具有通过 Azure 负载均衡器连接的多个子网。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）在中心辐射型拓扑中实现分支 VNet
> * 使用 Terraform 在分支网络中创建虚拟机
> * 使用 Terraform 与中心网络建立虚拟网络对等互连

## <a name="prerequisites"></a>先决条件

1. [在 Azure 中使用 Terraform 创建中心辐射型混合网络拓扑](./terraform-hub-spoke-introduction.md)。
1. [在 Azure 中使用Terraform 创建本地虚拟网络](./terraform-hub-spoke-on-prem.md)。
1. [在 Azure 中使用 Terraform 创建中心虚拟网络](./terraform-hub-spoke-hub-network.md)。
1. [在 Azure 中使用 Terraform 创建中心虚拟网络设备](./terraform-hub-spoke-hub-nva.md)。

## <a name="create-the-directory-structure"></a>创建目录结构

此部分创建两个分支脚本。 每个脚本为工作负荷定义一个分支虚拟网络和一个虚拟机。 然后创建一个从中心到分支的对等互连虚拟网络。

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

## <a name="declare-the-two-spoke-networks"></a>声明这两个分支网络

1. 在 Cloud Shell 中，打开名为 `spoke1.tf` 的新文件。

    ```bash
    code spoke1.tf
    ```

1. 在编辑器中粘贴以下代码：

    ```hcl
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = local.spoke1-resource-group
      location = local.spoke1-location
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke1-vnet-rg.name
      address_space       = ["10.1.0.0/16"]

      tags {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke1-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet" , "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke1
        subnet_id                     = azurerm_subnet.spoke1-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke1-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke1-nic.id]
      vm_size               = var.vmsize

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
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke1-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```

1. 保存文件并退出编辑器。

1. 创建名为 `spoke2.tf` 的新文件。

      ```bash
      code spoke2.tf
      ```
    
1. 在编辑器中粘贴以下代码：
    
    ```hcl
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = local.spoke2-resource-group
      location = local.spoke2-location
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke2-vnet-rg.name
      address_space       = ["10.2.0.0/16"]

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke2-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke2
        subnet_id                     = azurerm_subnet.spoke2-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke2-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke2-nic.id]
      vm_size               = var.vmsize

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
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke2-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```
     
1. 保存文件并退出编辑器。
  
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [在 Azure 中使用 Terraform 验证中心和分支网络](./terraform-hub-spoke-validation.md)