---
title: "使用 Terraform 并通过 HCL 创建 Azure VM 规模集。"
description: "使用 Terraform 配置 Azure 虚拟机规模集（配有虚拟网络和托管的附加磁盘）并对其进行版本控制。"
keywords: "terraform, devops, 规模集, 虚拟机, 网络, 存储, 模块"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>使用 Terraform 计划和创建联网的 Azure VM 规模集（含托管存储）

在本文中，你将使用 [Terraform](https://www.terraform.io/) 并通过 [Hashicorp 配置语言](https://www.terraform.io/docs/configuration/syntax.html) (HCL) 创建和部署含有托管磁盘的 [ Azure 虚拟机规模集](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview)。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Terraform 部署
> * 使用变量和输出部署 Terraform 
> * 创建和部署网络基础结构
> * 创建和部署虚拟机规模集并将其附加到网络
> * 创建和部署 jumpbox 以通过 SSH 连接到 VM

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

- [安装 Terraform 并配置对 Azure 的访问](/azure/virtual-machines/linux/terraform-install-configure)
- [创建 SSH 密钥对](/azure/virtual-machines/linux/mac-create-ssh-keys)（如果还没有）。

## <a name="create-the-file-structure"></a>创建文件结构

在空目录中使用以下名称创建 3 个新文件：

- `variables.tf` 此文件保存模板中所用的变量值。
- `output.tf` 此文件说明部署后将显示的设置。
- `vmss.tf` 此代码用于虚拟机规模集基础结构。

## <a name="create-the-variables-and-output-definitions"></a>创建变量和输出定义

在本步骤中，定义可用于自定义 Terraform 所创建资源的变量。

编辑 `variables.tf` 文件，复制下列代码，然后保存更改。

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

编辑 `output.tf` 文件并复制下列代码以公开虚拟机的完全限定域名。 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>在模板中定义网络基础结构

在本步骤中，你将在新的 Azure 资源组中创建以下网络基础结构： 

  - 1 个 VNET，地址空间为 10.0.0.0/16 
  - 1 个子网，地址空间为 10.0.2.0/24
  - 2 个公共 IP 地址。 一个供虚拟机规模集负载均衡器使用，另一个用于连接到 SSH jumpbox。


在 `vmss.tf` 文件中编辑并复制下列代码： 

```tf
resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> 最好对部署在 Azure 中的资源进行标记，便于将来识别它们。

## <a name="create-the-network-infrastructure"></a>创建网络基础结构

通过在创建 `.tf` 文件的目录中运行以下命令，初始化 Terraform 环境：

```bash
terraform init 
```

然后运行以下命令在 Azure 中部署基础结构。

```bash
terraform apply
```

验证公共 IP 地址的 FQDN 是否与配置相对应：![公共 IP 地址的 VMSS Terraform FQDN](./media/tf-create-vmss-step4-fqdn.png)


资源组应具有以下资源：![VMSS Terraform 网络资源](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>编辑基础结构以添加虚拟机规模集

在本步骤中，将以下资源添加到模板：

- 1 个 Azure 负载均衡器和规则，用于为应用程序提供服务并将其附加到之前配置的公共 IP 地址。
- Azure 后端地址池，并将其分配给负载均衡器 
- 运行状况探测端口，供应用程序使用并配置在负载均衡器上 
- 虚拟机规模集，位于在之前部署的 VNET 上运行的负载均衡器后端
- [Nginx](http://nginx.org/)，位于使用自定义脚本扩展的虚拟机规模集的节点上。

在 `vmss.tf` 文件的末尾添加以下代码。

```tf
resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
    resource_group_name            = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id                = "${azurerm_lb.vmss.id}"
    name                           = "http"
    protocol                       = "Tcp"
    frontend_port                  = "${var.application_port}"
    backend_port                   = "${var.application_port}"
    backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
    frontend_ip_configuration_name = "PublicIPAddress"
    probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
  }

  tags {
    environment = "codelab"
  }
}
```

通过将以下代码添加 `variables.tf` 自定义部署：

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>在 Azure 中部署虚拟机规模集

运行以下命令直观显示虚拟机规模集部署：

```bash
terraform plan
```

该命令的输出应如下所示。
![Terraform 添加 vmss 计划](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

然后在 Azure 中部署其他资源： 

```bash
terraform apply 
```

资源组的内容应如下所示：

![Terraform VM 规模集资源组](./media/tf-create-vmss-step6-apply.png)

打开浏览器并连接到该命令返回的 FQDN。 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>将 SSH jumpbox 添加到现有网络 

在本步骤中，配置以下资源：
- 网络接口，它已连接到与虚拟机规模集相同的子网。
- 虚拟机，它已连接到此网络接口。 此“jumpbox”可远程访问。 连接后，可通过 SSH 访问规模集中的任何虚拟机。



在 `vmss.tf` 文件的末尾添加以下代码：

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

编辑 `outputs.tf` 并添加以下代码，以在部署完成后显示 jumpbox 的主机名：

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>部署 jumpbox

部署 jumpbox。

```bash
terraform apply 
```

完成部署后，资源组的内容应如下所示：

![Terraform VM 规模集资源组](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> 部署的 jumpbox 和虚拟机规模集上禁止使用密码登录。 请使用 SSH 登录以访问 VM。

## <a name="clean-up-the-environment"></a>清理环境

以下命令可删除在本教程中创建的资源：

```bash
terraform destroy
```

系统要求确认删除资源时，请键入 `yes`。 破坏过程需要几分钟才能完成。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Terraform 在 Azure 上部署虚拟机规模集。 你已了解如何：

> [!div class="checklist"]
> * 初始化 Terraform 部署
> * 使用变量和输出部署 Terraform 
> * 创建和部署网络基础结构
> * 创建和部署虚拟机规模集并将其部署到现有环境
> * 创建和部署 jumpbox 以通过 SSH 连接到 VM 
