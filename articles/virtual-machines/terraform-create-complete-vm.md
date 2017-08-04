---
title: "使用 Terraform 在 Azure 中创建基本基础结构 | Microsoft Docs"
description: "了解如何使用 Terraform 创建 Azure 资源"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 3787151651767e594dae21f3cfbf79023fc6db40
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>使用 Terraform 在 Azure 中创建基本基础结构
本文介绍在 Azure 中预配虚拟机以及底层基础结构所要执行的步骤。 其中介绍了如何编写 Terraform 脚本，以及在云基础结构中实施更改之前如何可视化更改。 此外，还介绍了如何使用 Terraform 在 Azure 中创建基础结构。

若要开始，请在所选的文本编辑器（Visual Studio Code、Sublime、Vim 等）中创建名为 \_terraform_azure101.tf_ 的文件。 文件的确切名称并不重要，因为 terraform 接受使用文件夹名称作为参数：将执行文件夹中的所有脚本。 将以下代码粘贴到新文件中：

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
在脚本的 `provider` 节中，告知 Terraform 要使用某个 Azure 提供程序来预配脚本中的资源。 若要获取 subscription_id、client_id、client_secret 和 tenant_id 的值，请参阅[安装和配置 Terraform](terraform-install-configure.md) 指南。 如果为此块中的值创建了环境变量，则不需要包含这些值。 

`azurerm_resource_group` 资源指示 Terraform 创建新资源组。 本文稍后会列出 Terraform 中可用的其他资源。

## <a name="execute-the-script"></a>执行脚本
保存脚本后，请退出并转到控制台/命令行，键入以下命令：
```
terraform plan terraformscripts
```
假设 `terraformscripts` 是脚本所保存到的文件夹。 我们使用了 `plan` Terraform 命令来查找脚本中定义的资源。 该命令将这些资源与 Terraform 保存的状态进行比较，然后输出计划的执行，但不会在 Azure 中实际创建资源。 

执行上述命令后，应会出现类似于以下屏幕的内容：

![Terraform 计划](linux/media/terraform/tf_plan2.png)

如果一切看上去正常，请执行以下命令，在 Azure 中预配此新资源组： 
```
terraform apply terraformscripts
```
在 Azure 门户中，应会看到名为 `terraformtest` 的新空资源组。 在以下部分，我们将添加一个虚拟机，并将该虚拟机的所有支持基础结构添加到该资源组。

## <a name="provision-an-ubuntu-vm-with-terraform"></a>使用 Terraform 预配 Ubuntu VM
让我们扩展前面创建的 Terraform 脚本，并在其中包含预配运行 Ubuntu 的虚拟机所需的详细信息。 要在以下部分中预配的资源包括：

* 包含单个子网的网络
* 网络接口卡 
* 包含存储容器的存储帐户
* 公共 IP
* 利用上述所有资源的虚拟机 

有关每个 Azure Terraform 资源的详细文档，请查阅 [Terraform 文档](https://www.terraform.io/docs/providers/azurerm/index.html)。

为方便参考，此处还提供了[预配脚本](#complete-terraform-script)的完整版本。

### <a name="extend-the-terraform-script"></a>扩展 Terraform 脚本
使用以下资源扩展所创建的脚本： 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
上述脚本将创建虚拟网络及其包含的子网。 请在虚拟网络和子网的定义中，记下对已通过“${azurerm_resource_group.helloterraform.name}”创建的资源组的引用。

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
上述脚本片段将创建一个公共 IP，以及利用该 IP 的网络接口。 请记下对 subnet_id 和 public_ip_address_id 的引用。 Terraform 具有内置智能，可以识别到网络接口依赖于在创建该网络接口之前需要创建的资源。

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
此处已创建了一个存储帐户并在其中定义了一个存储容器。 将在此存储帐户中存储即将创建的虚拟机的虚拟硬盘 (VHD)。

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
~~~~
最后，上述片段会创建利用所有已预配资源的虚拟机。 这些资源包括 VHD 的存储帐户和容器、指定了公共 IP 和子网的网络接口，以及已创建的资源组。 请注意 vm_size 属性，脚本在其中指定了 Azure A0 SKU。

### <a name="execute-the-script"></a>执行脚本
保存整个脚本后，请退出并转到控制台/命令行，键入以下命令：
```
terraform apply terraformscripts
```
一段时间后，Azure 门户上的 `terraformtest` 资源组中应会显示资源，其中包括一个虚拟机。

## <a name="complete-the-terraform-script"></a>完成 Terraform 脚本

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
```

## <a name="next-steps"></a>后续步骤
现已使用 Terraform 在 Azure 中创建了基本基础结构。 有关更复杂的方案（包括使用负载均衡器和虚拟机规模集的示例），请参阅众多的[适用于 Azure 的 Terraform 示例](https://github.com/hashicorp/terraform/tree/master/examples)。 有关受支持 Azure 提供程序的最新列表，请参阅 [Terraform 文档](https://www.terraform.io/docs/providers/azurerm/index.html)。

