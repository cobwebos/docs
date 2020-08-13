---
title: 教程 - 使用 Terraform 预配 Azure Spring Cloud 实例
description: 使用 Terraform 预配 Azure Spring Cloud 实例。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 70a9d9f8b137f02e647d1ea0edddd409ec3cdaf3
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079186"
---
# <a name="tutorial-provision-an-azure-spring-cloud-instance-with-terraform"></a>教程：使用 Terraform 预配 Azure Spring Cloud 实例

本教程使用 Terraform 创建 Azure Spring Cloud 实例。 这些过程将引导你完成以下资源的创建：

> [!div class="checklist"]
> * 资源组
> * Azure Spring Cloud 实例
> * 适用于 Log Analytics 的 Azure 存储

> [!NOTE]
> 若要获取特定于 Terraform 的支持，请使用 HashiCorp 社区提供的 Terraform 支持渠道之一：
>
> * 问题、用例和有用的模式：[HashiCorp 社区门户的“Terraform”部分](https://discuss.hashicorp.com/c/terraform-core)
> * 与提供程序相关的问题：[HashiCorp 社区门户的“Terraform 提供程序”部分](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-configuration-file"></a>创建配置文件

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 打开 [Azure Cloud Shell](https://docs.microsoft.com/azure/app-service/quickstart-java#use-azure-cloud-shell)。

1. 启动 Cloud Shell 编辑器：

    ```bash
    code main.tf
    ```

1. 此步骤中的配置将对 Azure 资源进行建模，包括 Azure 资源组和 Azure Spring Cloud 实例。

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. 保存文件 ( **&lt;Ctrl>S**) 并退出编辑器 ( **&lt;Ctrl>Q**)。

## <a name="apply-the-configuration"></a>应用配置

在本部分中，请使用多个 Terraform 命令运行配置。

1. [terraform init](https://www.terraform.io/docs/commands/init.html) 命令初始化工作目录。 在 Cloud Shell 中运行以下命令：

    ```bash
    terraform init
    ```

1. 命令 [terraform plan](https://www.terraform.io/docs/commands/plan.html) 用于验证配置语法。 `-out` 参数可将结果定向到文件。 稍后可以使用输出文件应用配置。 在 Cloud Shell 中运行以下命令：

    ```bash
    terraform plan --out plan.out
    ```

1. 命令 [terraform apply](https://www.terraform.io/docs/commands/apply.html) 用于应用配置。 该命令指定上一步中的输出文件。 此命令创建 Azure 资源。 在 Cloud Shell 中运行以下命令：

    ```bash
    terraform apply plan.out
    ```

1. 若要在 Azure 门户中验证结果，请浏览到新资源组。 新的 Azure Cosmos DB 实例位于新的资源组中。

## <a name="update-configuration-to-config-logs-and-metrics"></a>更新配置以配置日志和指标

本部分演示如何更新配置，以使用 Azure 存储帐户启用 Azure Spring Cloud 的日志和指标。

1. 启动 Cloud Shell 编辑器：

    ```bash
    code main.tf
    ```

1. 在文件末尾添加以下配置：

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. 保存文件 (&lt;Ctrl>S) 并退出编辑器 (&lt;Ctrl>Q) 。

1. 如上一部分中所示，运行以下命令进行更改：

    ```bash
    terraform plan --out plan.out
    ```

1. 运行 `terraform apply` 命令以应用配置。

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。

运行 [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) 命令以删除在本教程中创建的 Azure 资源：

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装并配置 Terraform 以预配 Azure 资源](https://docs.microsoft.com/azure/developer/terraform/getting-started-cloud-shell)。