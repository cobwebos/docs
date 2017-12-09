---
title: "使用 Azure Cloud Shell 中的 Bash 通过 Terraform 进行部署 | Microsoft Docs"
description: "在 Bash 中使用 Terraform 部署 Azure 资源"
services: Azure
documentationcenter: 
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: c75b5d521dc3eacaf5c5921c35442b1afeb4fa13
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="terraform-and-bash-in-cloud-shell"></a>Terraform 和 Cloud Shell 中的 Bash
本文逐步讲解如何使用 [Terraform AzureRM 提供程序](https://www.terraform.io/docs/providers/azurerm/index.html)创建资源组。 

[Hashicorp Terraform](https://www.terraform.io/) 是一个开放工具，可将 API 编码成能够在团队成员之间共享的声明性配置文件，让他们对其进行编辑、审阅和版本控制。 使用 Microsoft AzureRM 提供程序可以通过 AzureRM API 来与 Azure 资源管理器支持的资源交互。 

## <a name="automatic-authentication"></a>自动化身份验证
Terraform 默认已安装在 Cloud Shell 中的 Bash 内。 此外，Cloud Shell 会自动对默认的 Azure CLI 2.0 订阅进行身份验证，以通过 Terraform Azure 模块部署资源。

Terraform 使用设置的默认 Azure CLI 2.0 订阅。 若要更新默认订阅，请运行：

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>演练
### <a name="launch-bash-in-cloud-shell"></a>启动 Cloud Shell 中的 Bash
1. 从首选的位置启动 Cloud Shell
2. 验证是否已设置首选订阅

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>创建 Terraform 模板
使用首选的文本编辑器创建名为 main.tf 的新 Terraform 模板。

```
vim main.tf
```

将以下代码复制/粘贴到 Cloud Shell 中。

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

保存文件并退出文本编辑器。

### <a name="terraform-init"></a>Terraform init
首先运行 `terraform init`。

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

[terraform init 命令](https://www.terraform.io/docs/commands/init.html)用于初始化包含 Terraform 配置文件的工作目录。 编写新的 Terraform 配置或者从版本控制中克隆现有的配置后，应该首先运行 `terraform init` 命令。 可以安全地运行此命令多次。

### <a name="terraform-plan"></a>Terraform 计划
使用 `terraform plan` 预览 Terraform 模板将要创建的资源。

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

[terraform plan 命令](https://www.terraform.io/docs/commands/plan.html)用于创建执行计划。 除非显式禁用，否则 Terraform 会执行刷新，然后确定需要执行哪些操作才能达到配置文件中指定的所需状态。 可以使用 -out 保存计划，然后将计划提供给 terraform apply，确保只执行预先计划的操作。

### <a name="terraform-apply"></a>Terraform apply
使用 `terraform apply` 预配 Azure 资源。

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[terraform apply 命令](https://www.terraform.io/docs/commands/apply.html)用于应用达到所需配置状态而需要执行的更改。

### <a name="verify-deployment-with-azure-cli-20"></a>使用 Azure CLI 2.0 验证部署
运行 `az group show -n myRgName` 验证是否已成功预配资源。

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>terraform destroy 进行清理
使用 [Terraform destroy 命令](https://www.terraform.io/docs/commands/destroy.html)清理创建的资源组，以清理 Terraform 创建的基础结构。

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

现已成功通过 Terraform 创建 Azure 资源。 请访问后续步骤继续了解 Cloud Shell。

## <a name="next-steps"></a>后续步骤
[了解 Terraform Azure 提供程序](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Cloud Shell 中的 Bash 快速入门](quickstart.md)