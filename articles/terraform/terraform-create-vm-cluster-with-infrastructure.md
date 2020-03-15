---
title: 教程 - 使用 Terraform 和 HCL 创建 Azure VM 群集
description: 本教程使用 Terraform 和 HCL 在 Azure 中创建具有负载均衡器的 Linux 虚拟机群集
keywords: azure devops terraform vm virtual machine cluster
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945273"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>教程：使用 Terraform 和 HCL 创建 Azure VM 群集

在本教程中，你将了解如何使用 [HCL](https://www.terraform.io/docs/configuration/syntax.html) 创建小型计算群集。 

你将了解如何执行以下任务：

> [!div class="checklist"]
> * 设置 Azure 身份验证。
> * 创建 Terraform 配置文件。
> * 使用 Terraform 配置文件创建负载平衡器。
> * 使用 Terraform 配置文件在可用性集内部署两个 Linux VM。
> * 初始化 Terraform。
> * 创建 Terraform 执行计划。
> * 应用 Terraform 执行计划以创建 Azure 资源。

## <a name="1-set-up-azure-authentication"></a>1.设置 Azure 身份验证

> [!NOTE]
> 如果[使用 Terraform 环境变量](terraform-install-configure.md)或在 [Azure Cloud Shell](terraform-cloud-shell.md) 中运行此教程，请跳过此部分。

此部分中，将生成 Azure 服务主体和两个包含安全性主体中的凭据的 Terraform 配置文件。

1. [设置 Azure AD 服务主体](terraform-install-configure.md#set-up-terraform-access-to-azure)，使 Terraform 将资源预配到 Azure。 创建主体时，请注意订阅 ID、租户、appId 和密码的值。

2. 打开命令提示符。

3. 创建一个空目录来存储 Terraform 文件。

4. 创建一个新文件来保存变量声明。 可将此文件命名为任何希望的且具有 `.tf` 扩展名的名称。

5. 将以下代码复制到变量声明文件中：

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. 创建一个包含 Terraform 变量的值的新文件。 当 Terraform 自动加载任意名为 `terraform.tfvars`（或遵循 `*.auto.tfvars` 模式）的文件时（如果当前目录中存在该文件），通常将对 Terraform 变量文件 `terraform.tfvars` 进行命名。 

7. 将以下代码复制到变量文件中。 请确保按如下所示替换占位符：对于 `subscription_id`，请使用运行 `az account set` 时指定的 Azure 订阅 ID。 对于 `tenant_id`，请使用从 `az ad sp create-for-rbac` 返回的 `tenant` 值。 对于 `client_id`，请使用从 `az ad sp create-for-rbac` 返回的 `appId` 值。 对于 `client_secret`，请使用从 `az ad sp create-for-rbac` 返回的 `password` 值。

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2.创建 Terraform 配置文件

此部分中，将创建包含基础结构资源定义的文件。

1. 创建名为 `main.tf` 的新文件。 

2. 将以下示例资源定义复制到新创建的 `main.tf` 文件中： 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3.初始化 Terraform 

使用 [terraform init command](https://www.terraform.io/docs/commands/init.html) 初始化包含 Terraform 配置文件（前一部分创建的文件）的目录。 在写入新的 Terraform 配置后始终运行 `terraform init` 命令是一个好的做法。 

> [!TIP]
> `terraform init` 命令是幂等的，这意味着可以在生成相同结果时重复调用。 所以，如果在协作环境中工作，并认为配置文件可能已经更改，那最好在执行或应用计划前调用 `terraform init` 命令。

若要初始化 Terraform，请运行以下命令：

  ```bash
  terraform init
  ```

  ![初始化 Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4.创建 Terraform 执行计划

[terraform plan 命令](https://www.terraform.io/docs/commands/plan.html)用于创建执行计划。 若要生成执行计划，Terraform 将在当前目录中聚合所有 `.tf` 文件。 

[-out 参数](https://www.terraform.io/docs/commands/plan.html#out-path)会将执行计划保存到输出文件中。 此功能解决了多开发环境中常见的并发问题。 输出文件解决的一个问题是以下情形：

1. 开发人员 1 创建配置文件。
1. 开发人员 2 修改配置文件。
1. 开发人员 1 应用（运行）配置文件。
1. 开发人员 1 会收到意外的结果，因为不知道开发人员 2 修改了配置。

开发人员 1 指定输出文件可阻止开发人员 2 影响开发人员 1。 

如果不需要保存执行计划，请运行以下命令：

  ```bash
  terraform plan
  ```

如果需要保存执行计划，请运行以下命令。 将占位符替换为你的环境的相应值。

  ```bash
  terraform plan -out=<path>
  ```

另一个有用的参数为 [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo)。

默认情况下，Terraform 尝试查找变量文件，如下所示：
- 名为 `terraform.tfvars` 的文件
- 使用以下模式命名的文件：`*.auto.tfvars`

但是，变量文件不需要遵循上述两个约定中的任何一个。 在此情况下，请使用 `-var-file` 参数指定变量文件名，其中变量文件名不带扩展名。 以下示例对此进行了说明：

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform 确定在配置文件中指定状态所需的操作。

![创建 Terraform 执行计划](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5.应用 Terraform 执行计划

本教程的最后一个步骤是使用 [terraform apply 命令](https://www.terraform.io/docs/commands/apply.html) 应用 `terraform plan` 命令生成的系列操作。

如果需要应用最新的执行计划，请运行以下命令：

  ```bash
  terraform apply
  ```

如果需要应用以前保存的执行计划，请运行以下命令。 将占位符替换为你的环境的相应值：

  ```bash
  terraform apply <path>
  ```

![应用 Terraform 执行计划](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [使用 Terraform 创建 Azure 虚拟机规模集](terraform-create-vm-scaleset-network-disks-hcl.md)