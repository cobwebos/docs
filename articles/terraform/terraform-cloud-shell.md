---
title: 将 Terraform 与 Azure Cloud Shell 结合使用
description: 将 Terraform 与 Azure Cloud Shell 结合使用可简化身份验证和模板配置。
keywords: terraform, devops, 规模集, 虚拟机, 网络, 存储, 模块
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5157066086f1bdfa580c1946942bda4505e48935
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121519"
---
# <a name="terraform-cloud-shell-development"></a>Terraform Cloud Shell 开发 

从 Bash 命令行（例如 macOS Terminal、Windows Bash 或 Linux Bash）使用时，Terraform 运行良好。 在 [Azure Cloud Shell](/azure/cloud-shell/overview) 的 Bash 体验中运行 Terraform 配置具有一些独特优势，可加速开发周期。

本概念文章涵盖了 Cloud Shell 功能，这些功能可帮助你编写可部署到 Azure 的 Terraform 脚本。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>自动凭据配置

Terraform 已安装并可立即在 Cloud Shell 中使用。 Terraform 脚本在登录 Cloud Shell 时通过 Azure 进行身份验证以管理基础架构，而无需任何其他配置。 借助自动身份验证，无需手动创建 Active Directory 服务主体和配置 Azure Terraform 提供程序变量。


## <a name="using-modules-and-providers"></a>使用模块和提供程序

Azure Terraform 模块需要凭据才能访问 Azure 订阅中的资源并对这些资源做出更改。 在 Cloud Shell 中工作时，将以下代码添加到脚本，以便在 Cloud Shell 中使用 Azure Terraform 模块：

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

使用任何 `terraform` CLI 命令时，Cloud Shell 会通过环境变量传递 `azurerm` 提供程序所需的值。

## <a name="other-cloud-shell-developer-tools"></a>其他 Cloud Shell 开发人员工具

文件和 shell 状态保留在 Cloud Shell 会话间的 Azure 存储中。 使用 [Azure 存储资源管理器](/azure/vs-azure-tools-storage-manage-with-storage-explorer)将文件从本地计算机复制和上传到 Cloud Shell。

Azure CLI 2.0 可在 Cloud Shell 中使用，是一款在 `terraform apply` 或 `terraform destroy` 完成后测试配置和检查工作的出色工具。


## <a name="next-steps"></a>后续步骤

[使用模块注册表创建小型 VM 群集](terraform-create-vm-cluster-module.md)
[使用自定义 HCL 创建小型 VM 群集](terraform-create-vm-cluster-with-infrastructure.md)
