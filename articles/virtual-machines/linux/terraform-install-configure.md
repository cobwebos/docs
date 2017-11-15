---
title: "安装和配置 Terraform 以在 Azure 中预配 VM 和其他基础结构 | Microsoft Docs"
description: "了解如何安装和配置用于创建 Azure 资源的 Terraform"
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
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: 533add8948544e37dc27623a2f51aad1054b1bef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>安装和配置 Terraform 以在 Azure 中预配 VM 和其他基础结构
 
借助 Terraform，可以轻松使用[简单模板语言](https://www.terraform.io/docs/configuration/syntax.html)来定义、预览和部署云基础结构。 本文介绍使用 Terraform 在 Azure 中预配资源的必要步骤。 

> [!TIP]
> [Bash in Azure Cloud Shell 体验中](/azure/cloud-shell/quickstart)默认安装 Terraform。 它还预先配置了凭据和 [Azure Terraform 模块](https://registry.terraform.io/modules/Azure)。 通过使用 Cloud Shell，可跳过本文档的安装/设置部分。

## <a name="install-terraform"></a>安装 Terraform

若要安装 Terraform，请将适用于操作系统的程序包[下载](https://www.terraform.io/downloads.html)到单独安装目录。 下载内容包含一个可执行文件，应为其定义全局路径。 有关如何在 Linux 和 Mac 上设置路径的说明，请转到[此网页](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)。 有关如何在 Windows 上设置路径的说明，请转到[此网页](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)。 

使用 `terraform` 目录验证路径配置。 输出中应会显示可用 Terraform 选项的列表：

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>设置 Terraform 对 Azure 的访问权限

配置 [Azure AD 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)，以使 Terraform 将资源预配到 Azure。 服务主体可向你授予 Terraform 脚本，使用凭据在 Azure 订阅中预配资源。

创建 Azure AD 应用程序和 Azure AD 服务主体的方法有许多种。 目前最简单快捷的方法是使用 Azure CLI 2.0，[可在 Windows、Linux 或 Mac 上下载并安装](/cli/azure/install-azure-cli)此工具。

发出以下命令，登录以管理 Azure 订阅：

   `az login`

如果有多个 Azure 订阅，`az login` 命令可返回其详细信息。 设置 `SUBSCRIPTION_ID` 环境变量，用于保存从要使用的订阅返回的 `id` 字段值。 

设置要用于此会话的订阅。

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

查询帐户，以获取订阅 ID 和租户 ID 值。

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

接下来，为 Terraform 创建单独凭据。

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

将返回 appId、password、sp_name 和 tenant。 记下 appId 和 password。

若要测试凭据，打开新的 shell 并运行以下命令，并对 sp_name、密码和租户使用返回的值：

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>配置 Terraform 环境变量

配置 Terraform 以在创建 Azure 资源时，从服务主体使用租户 ID、订阅 ID、客户端 ID 和客户端密码。 设置以下环境变量，[Azure Terraform 模块](https://registry.terraform.io/modules/Azure)将自动使用该变量。

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

可以使用此示例 shell 脚本设置这些变量：

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

## <a name="run-a-sample-script"></a>运行示例脚本

在空白目录下创建文件 `test.tf`，并粘贴到以下脚本中。 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

保存文件并运行 `terraform init`。 此命令将下载创建 Azure 资源组所需的 Azure 模块。 可以看到以下输出：

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

使用 `terraform plan` 预览脚本，然后使用 `terraform apply` 创建 `testResouceGroup` 资源：

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>后续步骤

已安装 Terraform 并配置 Azure 凭据，接下来可以开始将基础结构部署到 Azure 订阅。 然后，创建空的 Azure 资源组，测试安装。

> [!div class="nextstepaction"]
> [使用 Terraform 创建 Azure VM](terraform-create-complete-vm.md)

