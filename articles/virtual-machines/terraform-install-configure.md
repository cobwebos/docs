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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>安装和配置 Terraform 以在 Azure 中预配 VM 和其他基础结构 
本文详细介绍了安装和配置 Terraform 以在 Azure 中预配虚拟机等资源的必要步骤。 将了解如何创建和使用 Azure 凭据来启用 Terraform，从而安全地预配云资源。

借助 HashiCorp Terraform，可以轻松使用 HCL 这种自定义模板化语言来定义和部署云基础结构。 此自定义语言[易于编写和理解](terraform-create-complete-vm.md)。 此外，通过“terraform plan”命令，还可以使用 Terraform 在提交基础结构更改之前直观呈现这些更改。 请按照以下步骤操作，开始将 Terraform 与 Azure 结合使用。

## <a name="installing-terraform"></a>安装 Terraform
若要安装 Terraform，请将适用于操作系统的程序包[下载](https://www.terraform.io/downloads.html)到单独安装目录。 下载内容包含一个可执行文件，还应为其定义全局路径。 有关在 Linux 和 Mac 上设置路径的说明，请参阅[此网页](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)；有关在 Windows 上设置路径的说明，请参阅[此网页](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)。 通过运行“terraform”命令来验证安装，应该会输出可用 Terraform 选项列表。

接下来，需要允许 Terraform 访问 Azure 订阅，以便预配基础结构。

## <a name="setting-up-terraform-access-to-azure"></a>设置 Terraform 对 Azure 的访问权限
若要启用 Terraform 以在 Azure 中预配资源，需要在 Azure Active Directory (AAD) 中创建两个实体，即 AAD 应用程序和 AAD 服务主体。 然后，在 Terraform 脚本中使用这些实体的标识符。 服务主体是全局 AAD 应用程序的本地实例。 创建服务主体，可以在本地精细控制对全局资源的访问。

创建 AAD 应用程序和 AAD 服务主体的方法有许多种。 目前最简单快捷的方法是使用 Azure CLI 2.0，[可以在 Windows/Linux/Mac 上下载并安装此工具](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。 还可以使用 Powershell 或 Azure CLI 1.0，创建必要的安全基础结构。 下面介绍了如何使用所有这些方法为 Azure 配置 Terraform。

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>使用 Azure CLI 2.0 的 Windows/Linux/Mac 用户
下载并安装 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 后，发出以下命令，登录以管理 Azure 订阅

```
az login
```

>[!NOTE]
>如果使用的是中文版、德语版或政府版 Azure 云，需要先配置 Azure CLI 才能使用云。 为此，可以运行下列命令：

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

如果有多个 Azure 订阅，`az login` 命令可返回它们的详细信息。 设置 `SUBSCRIPTION_ID` 环境变量，以保存从要使用的订阅返回的 `id` 字段值。 

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

输出包含 client_id、client_secret（密码）、sp_name 和 tenant。 记下“client_id”和“client_secret”。

可以打开新的 shell，并运行以下命令（用返回值替换“sp_name”、“client_secret”和“tenant”），从而确认凭据（服务主体）：

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>使用 Powershell 的 Windows 用户
如果是使用 Windows 计算机编写和执行 Terraform 脚本，并希望将 Powershell 用于配置任务，则需要使用合适的 Powershell 工具来配置计算机。 为此，请 (1) [安装 Azure PowerShell 工具](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)；(2) 通过 Powershell 控制台下载并执行 [azure-setup.ps1 脚本](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)。 若要运行 azure-setup.ps1 脚本，请通过 Powershell 控制台下载并执行“./azure-setup.ps1 setup”命令，再使用管理权限登录 Azure 订阅。 当出现提示时，必须提供应用程序名称（任意字符串），并根据需要提供强密码。 如果未提供密码，将使用 .Net 安全库为用户生成强密码。

### <a name="linuxmac-users-using-azure-cli-10"></a>使用 Azure CLI 1.0 的 Linux/Mac 用户
若要开始在包含 Azure CLI 1.0 的 Linux 计算机或 Mac 上使用 Terraform，必须确保计算机上安装有正确的库。 为此，请 (1) [安装 Azure xPlat CLI 工具](https://docs.microsoft.com/cli/azure/install-azure-cli)；(2) [下载并安装 jq](https://stedolan.github.io/jq/download/) JSON 处理器；(3) 通过控制台下载并执行 [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) bash 脚本。 若要运行 azure-setup.sh 脚本，请通过控制台下载并执行“./azure-setup setup”命令，再使用管理权限登录 Azure 订阅。 当出现提示时，必须提供应用程序名称（任意字符串），并根据需要提供强密码。 如果未提供密码，将使用 .Net 安全库为用户生成强密码。

上面所有脚本都将创建 AAD 应用程序和服务主体，为服务主体提供对订阅的参与者或所有者级别访问权限。 由于授予的访问权限级别高，因此应始终保护由这些脚本生成的安全信息。 记下这些脚本提供的安全信息的所有四个部分：client_id、client_secret、subscription_id 和 tenant_id。 

## <a name="setting-environment-variables"></a>设置环境变量
创建和配置 AAD 服务主体后，需要告知 Terraform 要使用的租户 ID、订阅 ID、客户端 ID 和客户端密钥。 为此，可以在 Terraform 脚本中嵌入这些值（如[下一部分](terraform-create-complete-vm.md)所述）。 也可以设置下列环境变量（这样可以避免意外签入/共享凭据）：

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

下面展示了可用于设置这些变量的示例 shell 脚本：

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

此外，若要将 Terraform 与 Azure 政府、Azure 德国或 Azure 中国结合使用，需要设置相应的环境变量。

## <a name="next-steps"></a>后续步骤
至此，已安装 Terraform 并配置 Azure 凭据，可以开始将基础结构部署到 Azure 订阅了。 接下来，了解如何[使用 Terraform 创建基础结构](terraform-create-complete-vm.md)。
