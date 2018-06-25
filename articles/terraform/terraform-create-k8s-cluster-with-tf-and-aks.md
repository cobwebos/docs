---
title: 使用 Azure Kubernetes 服务 (AKS) 和 Terraform 创建 Kubernetes 群集
description: 演示如何使用 Azure Kubernetes 服务和 Terraform 创建 Kubernetes 群集的教程
keywords: terraform, devops, 虚拟机, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/11/2018
ms.topic: article
ms.openlocfilehash: bd00a0cc8446802a03570edd58949a46c0769101
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302361"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>使用 Azure Kubernetes 服务和 Terraform 创建 Kubernetes 群集
[Azure Kubernetes 服务 (AKS)](/azure/aks/) 管理托管的 Kubernetes 环境，使用户无需具备容器业务流程专业知识即可快速、轻松地部署和管理容器化的应用程序。 它还通过按需预配、升级和缩放资源，消除了正在进行的操作和维护的负担，而无需使应用程序脱机。

本教程介绍如何使用 [Terraform](http://terraform.io) 和 AKS 来执行创建 [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) 群集的任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）定义 Kubernetes 群集
> * 使用 Terraform 和 AKS 创建 Kubernetes 群集
> * 使用 kubectl 工具测试 Kubernetes 群集的可用性

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **配置 Terraform**：遵循[配置 Terraform 以及对 Azure 的访问](/azure/virtual-machines/linux/terraform-install-configure)一文中的指导

- **Azure 服务主体**：遵循[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)一文的“创建服务主体”部分中的指导。 记下 appId、displayName、password 和 tenant 的值。

## <a name="create-the-directory-structure"></a>创建目录结构
首先，创建包含 Terraform 配置文件的目录用于练习。

1. 浏览到 [Azure 门户](http://portal.azure.com)。

1. 打开 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果事先未选择环境，请选择“Bash”作为环境。

    ![Cloud Shell 提示符](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. 切换到 `clouddrive` 目录。

    ```bash
    cd clouddrive
    ```

1. 创建名为 `terraform-aks-k8s` 的目录。

    ```bash
    mkdir terraform-aks-k8s
    ```

1. 将目录切换到新目录：

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>声明 Azure 提供程序
创建声明 Azure 提供程序的 Terraform 配置文件。

1. 在 Cloud Shell 中，创建名为 `main.tf` 的文件。

    ```bash
    vi main.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：

    ```JSON
    provider "azurerm" {
        version = "=1.5.0"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>定义 Kubernetes 群集
创建 Terraform 配置文件，用于声明 Kubernetes 群集的资源。

1. 在 Cloud Shell 中，创建名为 `k8s.tf` 的文件。

    ```bash
    vi k8s.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_D2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    上面的代码设置群集的名称、位置和 resource_group_name。 此外，设置了 dns_prefix 值 - 构成了用于访问群集的完全限定域名 (FQDN) 的一部分。

    使用 **linux_profile** 记录可以配置用于通过 SSH 登录到工作节点的设置。

    使用 AKS 时，只需支付工作节点的费用。 **agent_pool_profile** 记录配置这些工作节点的详细信息。 **agent_pool_profile 记录**包含要创建的工作节点数，以及工作节点的类型。 如果将来需要纵向扩展或缩减群集，请修改此记录中的 **count** 值。

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>声明变量

1. 在 Cloud Shell 中，创建名为 `variables.tf` 的文件。

    ```bash
    vi variables.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }
    ```

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>创建 Terraform 输出文件
使用 [Terraform 输出](https://www.terraform.io/docs/configuration/outputs.html)可以定义当 Terraform 应用计划时要向用户突出显示的、可以使用 `terraform output` 命令查询的值。 在本部分，我们将创建一个输出文件，以便使用 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 访问群集。

1. 在 Cloud Shell 中，创建名为 `output.tf` 的文件。

    ```bash
    vi output.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>将 Azure 存储设置为存储 Terraform 状态
Terraform 在本地通过 `terraform.tfstate` 文件跟踪状态。 在单用户环境中，此模式非常合适。 但是，在更常见的多用户环境中，需要利用 [Azure 存储](/azure/storage/)来跟踪服务器上的状态。 在本部分，我们将检索所需的存储帐户信息（帐户名称和帐户密钥），并创建用于存储 Terraform 状态信息的存储容器。

1. 在 Azure 门户的左侧菜单中，选择“所有服务”。

1. 选择“存储帐户”。

1. 在“存储帐户”选项卡上，选择用于存储 Terraform 状态信息的存储帐户名称。 例如，可以使用首次打开 Cloud Shell 时创建的存储帐户。  Cloud Shell 创建的存储帐户名称通常以 `cs` 开头，后接由数字和字母组成的随机字符串。 **请记住选择的存储帐户名称，因为稍后需要用到。**

1. 在存储帐户选项卡上，选择“访问密钥”。

    ![存储帐户菜单](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. 记下“密钥 1”密钥值。 （选择密钥右侧的图标将值复制到剪贴板。）

    ![存储帐户访问密钥](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. 在 Cloud Shell 中，在 Azure 存储帐户内创建一个容器（请将 &lt;YourAzureStorageAccountName> 和 &lt;YourAzureStorageAccountAccessKey> 占位符替换为 Azure 存储帐户的相应值）。

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>创建 Kubernetes 群集
本部分介绍如何使用 `terraform init` 命令来创建资源，这些资源定义了前面部分中所创建的配置文件。

1. 在 Cloud Shell 中启动 Terraform（请将 &lt;YourAzureStorageAccountName> 和 &lt;YourAzureStorageAccountAccessKey> 占位符替换为 Azure 存储帐户的相应值）。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` 命令显示成功初始化后端和提供程序插件：

    ![“Terraform init”结果示例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. 运行 `terraform plan` 命令，以创建定义基础结构元素的 Terraform 计划。 该命令将请求两个值：**var.client_id** 和 **var.client_secret**。 对于 **var.client_id** 变量，请输入与服务主体关联的 **appId** 值。 对于 **var.client_secret** 变量，请输入与服务主体关联的 **password** 值。

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 命令显示运行 `terraform apply` 命令时要创建的资源：

    ![“Terraform plan”结果示例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. 运行 `terraform apply` 命令，以应用该计划来创建 Kubernetes 群集。 创建 Kubernetes 群集的过程可能需要花费几分钟时间，从而导致 Cloud Shell 会话超时。如果 Cloud Shell 会话超时，可以遵循[在 Cloud Shell 超时后进行恢复](#recover-from-a-dloud-shell-timeout)部分中的步骤，以完成本教程。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 命令显示创建配置文件中定义的资源的结果：

    ![“Terraform apply”结果示例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. 在 Azure 门户的左侧菜单中选择“所有服务”，查看为新 Kubernetese 群集创建的资源。

    ![Cloud Shell 提示符](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>在 Cloud Shell 超时后进行恢复
如果 Cloud Shell 会话超时，可执行以下步骤予以恢复：

1. 启动 Cloud Shell 会话。

1. 切换到包含 Terraform 配置文件的目录。

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 运行以下命令：

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>测试 Kubernetes 群集
可以使用 Kubernetes 工具来验证新建的群集。

1. 从 Terraform 状态中获取 Kubernetes 配置，并将其存储在 kubectl 可以读取的文件中。

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. 设置环境变量，使 kubectl 拾取正确的配置。

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. 验证群集的运行状况。

    ```bash
    kubectl get nodes
    ```

    应会看到工作节点的详细信息，并且这些节点的状态为“就绪”，如下图所示：

    ![使用 kubectl 工具可以验证 Kubernetes 群集的运行状况](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>后续步骤
本文已介绍如何使用 Terraform 和 AKS 创建 Kubernetes 群集。 请参阅以下附加资源，帮助自己详细了解 Azure 上的 Terraform： 

 [Microsoft.com 中的 Terraform 中心](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure 提供程序文档](http://aka.ms/terraform)  
 [Terraform Azure 提供程序源](http://aka.ms/tfgit)  
 [Terraform Azure 模块](http://aka.ms/tfmodules)