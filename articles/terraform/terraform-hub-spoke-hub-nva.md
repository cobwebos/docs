---
title: 教程 - 在 Azure 中使用 Terraform 创建中心虚拟网络设备
description: 教程实现了中心 VNet 的创建，中心 VNet 充当所有其他网络之间的公共连接点
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 28ccb89d237cbe21dd0433da5f7fbb32883f6550
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159245"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-in-azure-using-terraform"></a>教程：在 Azure 中使用 Terraform 创建中心虚拟网络设备

VPN 设备是用于向本地网络提供外部连接的设备  。 VPN 设备可以是硬件设备或软件解决方案。 软件解决方案的一个示例为 Windows Server 2012 中的路由和远程访问服务 (RRAS)。 有关 VPN 设备的详细信息，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备](/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

Azure 支持各种网络虚拟设备，可供选择。 本教程使用 Ubuntu 映像。 若要详细了解 Azure 中支持的多种设备解决方案，请参阅[网络设备主页](https://azure.microsoft.com/solutions/network-appliances/)。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）在中心辐射型拓扑中实现中心 VNet
> * 使用 Terraform 创建充当设备的中心网络虚拟机
> * 使用 Terraform 启用使用 CustomScript 扩展的路由
> * 使用 Terraform 创建中心辐射型网关路由表

## <a name="prerequisites"></a>先决条件

1. [在 Azure 中使用 Terraform 创建中心辐射型混合网络拓扑](./terraform-hub-spoke-introduction.md)。
1. [在 Azure 中使用Terraform 创建本地虚拟网络](./terraform-hub-spoke-on-prem.md)。
1. [在 Azure 中使用 Terraform 创建中心虚拟网络](./terraform-hub-spoke-hub-network.md)。

## <a name="create-the-directory-structure"></a>创建目录结构

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

## <a name="declare-the-hub-network-appliance"></a>声明中心网络设备

创建声明本地虚拟网络的 Terraform 配置文件。

1. 在 Cloud Shell 中，创建名为 `hub-nva.tf` 的新文件。

    ```bash
    code hub-nva.tf
    ```

1. 在编辑器中粘贴以下代码：
    
    ```hcl
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "CentralUS"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = local.hub-nva-location

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = azurerm_resource_group.hub-nva-rg.location
      resource_group_name  = azurerm_resource_group.hub-nva-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub-nva
        subnet_id                     = azurerm_subnet.hub-dmz.id
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = azurerm_resource_group.hub-nva-rg.location
      resource_group_name   = azurerm_resource_group.hub-nva-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nva-nic.id]
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
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      location             = azurerm_resource_group.hub-nva-rg.location
      resource_group_name  = azurerm_resource_group.hub-nva-rg.name
      virtual_machine_name = azurerm_virtual_machine.hub-nva-vm.name
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"

      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = azurerm_subnet.hub-gateway-subnet.id
      route_table_id = azurerm_route_table.hub-gateway-rt.id
      depends_on = ["azurerm_subnet.hub-gateway-subnet"]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke1-mgmt.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = ["azurerm_subnet.spoke1-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke1-workload.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = ["azurerm_subnet.spoke1-workload"]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke2-mgmt.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = ["azurerm_subnet.spoke2-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke2-workload.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = ["azurerm_subnet.spoke2-workload"]
    }

    ```

1. 保存文件并退出编辑器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure 中使用 Terraform 创建辐射虚拟网络](./terraform-hub-spoke-spoke-network.md)