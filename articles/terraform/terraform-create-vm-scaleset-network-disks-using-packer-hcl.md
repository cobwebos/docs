---
title: "使用 Terraform 通过 Packer 自定义映像创建 Azure 虚拟机规模集"
description: "使用 Terraform 通过 Packer 生成的自定义映像配置 Azure 虚拟机规模集（配有虚拟网络和托管的附加磁盘）并对其进行版本控制。"
keywords: "terraform, devops, 规模集, 虚拟机, 网络, 存储, 模块, 自定义映像, packer"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>使用 Terraform 通过 Packer 自定义映像创建 Azure 虚拟机规模集

在本教程中，你将使用 [Terraform](https://www.terraform.io/) 并通过 [HashiCorp 配置语言](https://www.terraform.io/docs/configuration/syntax.html) (HCL) 创建和部署含有托管磁盘的 [ Azure 虚拟机规模集](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview)（使用 [Packer](https://www.packer.io/intro/index.html) 生成的自定义映像创建）。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Terraform 部署
> * 使用变量和输出部署 Terraform 
> * 创建和部署网络基础结构
> * 使用 Packer 创建自定义虚拟机映像
> * 使用自定义映像创建和部署虚拟机规模集
> * 创建和部署 jumpbox 

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前
> * [安装 Terraform 并配置对 Azure 的访问](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [创建 SSH 密钥对](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)（如果还没有）
> * 如果尚未在本地计算机上安装 Packer，请[安装 Packer](https://www.packer.io/docs/install/index.html)


## <a name="create-the-file-structure"></a>创建文件结构

在空目录中使用以下名称创建 3 个新文件：

- ```variables.tf``` 此文件保存模板中所用的变量值。
- ```output.tf``` 此文件说明部署后显示的设置。
- ```vmss.tf``` 此文件包含要部署的基础结构的代码。

##  <a name="create-the-variables"></a>创建变量 

在本步骤中，定义可用于自定义 Terraform 所创建资源的变量。

编辑 `variables.tf` 文件，复制下列代码，然后保存更改。

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> 系统不会设置 resource_group_name 变量的默认值，请定义自己的值。

保存文件。

部署 Terraform 模板时，需要获得用于访问应用程序的完全限定域名。 使用 Terraform 的 ```output``` 资源类型并获取资源的 ```fqdn``` 属性。 

编辑 `output.tf` 文件并复制下列代码，公开虚拟机的完全限定域名。 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>在模板中定义网络基础结构 

在本步骤中，你将在新的 Azure 资源组中创建以下网络基础结构： 
  - 1 个 VNET，地址空间为 10.0.0.0/16 
  - 1 个子网，地址空间为 10.0.2.0/24
  - 2 个公共 IP 地址。 一个供虚拟机规模集负载均衡器使用；另一个用于连接到 SSH jumpbox

还需要在其中创建所有资源的资源组。 

在 ```vmss.tf``` 文件中编辑并复制下列代码： 

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
> 建议对在 Azure 中部署的资源进行标记，便于将来进行识别。

## <a name="create-the-network-infrastructure"></a>创建网络基础结构

通过在创建 `.tf` 文件的目录中运行以下命令，初始化 Terraform 环境：

```bash
terraform init 
```
 
提供程序插件从 Terraform 注册表将内容下载到运行命令所在目录的 ```.terraform``` 文件夹中。

运行以下命令以在 Azure 中部署基础结构。

```bash
terraform apply
```

验证公共 IP 地址的完全限定域名是否与配置相对应。

![公共 IP 地址的虚拟机规模集 Terraform 完全限定域名](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

资源组包含以下资源：

![虚拟机规模集 Terraform 网络资源](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>使用 Packer 创建 Azure 映像
使用教程[如何使用 Packer 在 Azure 中创建 Linux 虚拟机映像](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer)中所述的步骤创建自定义 Linux 映像。
 
按照本教程创建已取消设置且已安装 NGINX 的 Ubuntu 映像。

![创建 Packer 映像后即可拥有一个映像](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> 为达到本教程的目的，Packer 映像中将运行一个用于安装 nginx 的命令。 创建期间也可以运行自己的脚本。

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>编辑基础结构以添加虚拟机规模集

在此步骤中，需在以前部署的网络上创建以下资源：
- Azure 负载均衡器，用于为应用程序提供服务并将其附加到在步骤 4 创建的公共 IP 地址
- 1 个 Azure 负载均衡器和规则，用于为应用程序提供服务并将其附加到之前配置的公共 IP 地址。
- Azure 后端地址池，将其分配给负载均衡器 
- 运行状况探测端口，供应用程序使用并在负载均衡器上配置 
- 虚拟机规模集，位于在之前部署的 VNET 上运行的负载均衡器后端
- [Nginx](http://nginx.org/)，位于通过自定义映像安装的虚拟机规模集的节点上


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

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
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
    id="${data.azurerm_image.image.id}"
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

此命令的输出与以下图像类似：

![Terraform 添加虚拟机规模集计划](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

在 Azure 中部署其他资源： 

```bash
terraform apply 
```

资源组的内容与以下图像类似：

![Terraform 虚拟机规模集资源组](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

打开浏览器并连接到该命令返回的完全限定域名。 


## <a name="add-a-jumpbox-to-the-existing-network"></a>将 jumpbox 添加到现有网络 

通过此可选步骤，SSH 可使用 jumpbox 访问虚拟机规模集的实例。

将以下资源添加到现有部署：
- 网络接口，已连接到与虚拟机规模集相同的子网
- 虚拟机，已连接到此网络接口

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

完成部署后，资源组的内容应与以下图像类似：

![Terraform 虚拟机规模集资源组](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> 部署的 jumpbox 和虚拟机规模集上禁止使用密码登录。 请使用 SSH 登录以访问 VM。

## <a name="clean-up-the-environment"></a>清理环境

以下命令可删除在本教程中创建的资源：

```bash
terraform destroy
```

系统要求确认删除资源时，请键入 `yes`。 破坏过程可能需要几分钟才能完成。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Terraform 在 Azure 上部署了虚拟机规模集和 jumpbox。 你已了解如何：

> [!div class="checklist"]
> * 初始化 Terraform 部署
> * 使用变量和输出部署 Terraform 
> * 创建和部署网络基础结构
> * 使用 Packer 创建自定义虚拟机映像
> * 使用自定义映像创建和部署虚拟机规模集
> * 创建和部署 jumpbox 