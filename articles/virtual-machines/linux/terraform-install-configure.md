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
ms.date: 06/14/2017
ms.author: echuvyrov
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>安装和配置 Terraform 以在 Azure 中预配 VM 和其他基础结构 
本文详细介绍安装和配置 Terraform 以在 Azure 中预配虚拟机等资源的必要步骤。 将了解如何创建和使用 Azure 凭据来启用 Terraform，从而安全地预配云资源。

借助 HashiCorp Terraform，可以轻松使用 HashiCorp 配置语言 (HCL) 这种自定义模板化语言来定义和部署云基础结构。 此自定义语言[易于编写和理解](terraform-create-complete-vm.md)。 此外，还可以使用 `terraform plan` 命令在提交基础结构更改之前直观呈现这些更改。 请遵循以下步骤开始在 Azure 中使用 Terraform。

## <a name="install-terraform"></a>安装 Terraform
若要安装 Terraform，请将适用于操作系统的程序包[下载](https://www.terraform.io/downloads.html)到单独安装目录。 下载内容包含一个可执行文件，应为其定义全局路径。 有关如何在 Linux 和 Mac 上设置路径的说明，请转到[此网页](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)。 有关如何在 Windows 上设置路径的说明，请转到[此网页](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)。 若要验证安装，请运行 `terraform` 命令。 输出中应会显示可用 Terraform 选项的列表。

接下来，需要允许 Terraform 访问 Azure 订阅，以便预配基础结构。

## <a name="set-up-terraform-access-to-azure"></a>设置 Terraform 对 Azure 的访问权限
要使 Terraform 能够在 Azure 中预配资源，需要在 Azure Active Directory (Azure AD) 中创建两个实体：Azure AD 应用程序和 Azure AD 服务主体。 然后，在 Terraform 脚本中使用这些实体的标识符。 服务主体是全局 Azure AD 应用程序的本地实例。 使用服务主体可对全局资源进行精细的本地访问控制。

创建 Azure AD 应用程序和 Azure AD 服务主体的方法有许多种。 目前最简单快捷的方法是使用 Azure CLI 2.0，[可在 Windows、Linux 或 Mac 上下载并安装](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)此工具。 还可以使用 PowerShell 或 Azure CLI 1.0 创建必要的安全基础结构。 下面说明如何使用所有这些方法为 Azure 配置 Terraform。

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>使用 Azure CLI 2.0（适用于 Windows、Linux 或 Mac 用户） 
下载并安装 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 后，发出以下命令，登录以管理 Azure 订阅：

```
az login
```

>[!NOTE]
>如果使用中文版、Azure 德语版或 Azure 政府云，需要先配置 Azure CLI 才能使用该云。 可以运行以下代码来实现此目的：

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

如果有多个 Azure 订阅，`az login` 命令可返回其详细信息。 设置 `SUBSCRIPTION_ID` 环境变量，用于保存从要使用的订阅返回的 `id` 字段值。 

设置要用于此会话的订阅。

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

查询帐户，以获取订阅 ID 和租户 ID 值。

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

接下来，为 Terraform 创建单独凭据。

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

将返回 appId、password、sp_name 和 tenant。 记下 appId 和 password。

若要确认凭据（服务主体），请打开新的 shell 并运行以下命令： 请将 sp_name、password 和 tenant 替换为返回值：

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>使用 PowerShell（适用于 Windows 用户） 
若要使用 Windows 计算机编写和执行 Terraform 脚本，并将 PowerShell 用于配置任务，请使用合适的 PowerShell 工具来配置计算机。 

1. 遵循[安装和配置 Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) 中的步骤安装 PowerShell 工具。 

2. 通过 PowerShell 控制台下载并执行 [azure-setup.ps1 脚本](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)。

3. 若要运行 azure-setup.ps1 脚本，请通过 PowerShell 控制台下载该脚本并执行 `./azure-setup.ps1 setup` 命令。 然后使用管理特权登录到 Azure 订阅。

4. 出现提示时，提供应用程序名称（所需的任意字符串）。 （可选）出现提示时，提供强密码。 如果未提供密码，系统会使用 .NET 安全库生成强密码。

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>使用 Azure CLI 1.0（适用于 Linux 或 Mac 用户）
若要在包含 Azure CLI 1.0 的 Linux 计算机或 Mac 上开始使用 Terraform，请在计算机上安装适当的库。  

1. 遵循[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 中的步骤安装 Azure xPlat CLI 工具。 

2. 遵循[下载 jq](https://stedolan.github.io/jq/download/) 中的说明下载并安装 JSON 处理器。

3. 通过控制台下载并执行 [azure-setup.sh 脚本](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) bash 脚本。

4. 若要运行 azure-setup.sh 脚本，请通过控制台下载该脚本并执行 `./azure-setup setup` 命令。 然后使用管理特权登录到 Azure 订阅。
 
5. 出现提示时，提供应用程序名称（所需的任意字符串）。 （可选）出现提示时，提供强密码。 如果未提供密码，系统会使用 .NET 安全库生成强密码。

上述所有脚本会创建 Azure AD 应用程序和服务主体。 服务主体会在订阅中获取参与者或所有者级别的访问权限。 由于授予的访问权限级别高，因此应始终保护由这些脚本生成的安全信息。 记下这些脚本提供的安全信息的所有四个部分：appId、password、subscription_id 和 tenant_id。

## <a name="set-environment-variables"></a>设置环境变量。
创建并配置 Azure AD 服务主体之后，需要告知 Terraform 要使用的租户 ID、订阅 ID、客户端 ID 和客户端机密。 为此，可以根据[使用 Terraform 创建基本基础结构](terraform-create-complete-vm.md)中所述，在 Terraform 脚本中嵌入这些值。 或者，可以设置以下环境变量（这样可以避免意外签入或共享凭据）：

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

可以使用此示例 shell 脚本设置这些变量：

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

此外，如果将 Terraform 与中国区 Azure 或者 Azure 政府或 Azure 德国配合使用，需要相应地设置 ENVIRONMENT 变量。

## <a name="next-steps"></a>后续步骤
现已安装 Terraform 并配置 Azure 凭据，接下来可以开始将基础结构部署到 Azure 订阅。 接下来，了解如何[使用 Terraform 创建基础结构](terraform-create-complete-vm.md)。
