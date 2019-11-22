---
title: 教程 - 使用 Terraform 验证 Azure 中的中心辐射型网络
description: 有关使用所有互联的虚拟网络验证中心辐射型网络拓扑的教程。
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: b0b761fcd79f7129befefa37ce11d9c70cf7cb96
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969333"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>教程：使用 Terraform 验证 Azure 中的中心辐射型网络

在本文中，我们将执行在本教程系列的前一篇文章中创建的 Terraform 文件。 结果是演示虚拟网络之间的连接的验证结果。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）在中心辐射型拓扑中实现中心 VNet
> * 使用 Terraform 计划验证要部署的资源
> * 使用 Terraform apply 在 Azure 中创建资源
> * 验证不同网络之间的连接
> * 使用 Terraform 销毁所有资源

## <a name="prerequisites"></a>先决条件

1. [在 Azure 中使用 Terraform 创建中心辐射型混合网络拓扑](./terraform-hub-spoke-introduction.md)。
1. [在 Azure 中使用Terraform 创建本地虚拟网络](./terraform-hub-spoke-on-prem.md)。
1. [在 Azure 中使用 Terraform 创建中心虚拟网络](./terraform-hub-spoke-hub-network.md)。
1. [在 Azure 中使用 Terraform 创建中心虚拟网络设备](./terraform-hub-spoke-hub-nva.md)。
1. [在 Azure 中使用 Terraform 创建辐射虚拟网络](./terraform-hub-spoke-spoke-network.md)。

## <a name="verify-your-configuration"></a>验证配置

满足[先决条件](#prerequisites)后，验证相应的配置文件是否存在。

1. 浏览到 [Azure 门户](https://portal.azure.com)。

1. 打开 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果事先未选择环境，请选择“Bash”作为环境。 

    ![Cloud Shell 提示符](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. 切换到 `clouddrive` 目录。

    ```bash
    cd clouddrive
    ```

1. 将目录切换到新目录：

    ```bash
    cd hub-spoke
    ```

1. 运行 `ls` 命令，验证在前面教程中创建的 `.tf` 配置文件是否已列出：

    ![Terraform 演示配置文件](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>部署资源

1. 初始化 Terraform 提供程序：
    
    ```bash
    terraform init
    ```
    
    ![“Terraform init”命令的示例结果](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. 在执行部署之前，运行 `terraform plan` 命令查看部署效果：

    ```bash
    terraform plan
    ```
    
    ![“Terraform plan”命令的示例结果](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. 部署解决方案：

    ```bash
    terraform apply
    ```
    
    出现提示时，输入 `yes` 以确认部署。

    ![“Terraform apply”命令的示例结果](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>测试中心 VNet 和辐射 VNet

本部分介绍如何测试从模拟本地环境到中心 VNet 的连接。

1. 在 Azure 门户中，浏览到“onprem-vnet-rg”资源组。 

1. 在“onprem-vnet-rg”选项卡中，选择名为“onprem-vm”的 VM。  

1. 选择“连接”  。

1. 在“使用 VM 本地帐户登录”文本的旁边，将 **ssh** 命令复制到剪贴板。 

1. 在 Linux 提示符下，运行 `ssh` 连接到模拟本地环境。 使用 `on-prem.tf` 参数文件中指定的密码。

1. 运行 `ping` 命令，以测试与中心 VNet 中 Jumpbox VM 的连接。

   ```bash
   ping 10.0.0.68
   ```

1. 运行 `ping` 命令，以测试与每个辐射中 Jumpbox VM 的连接。

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. 若要在 **onprem-vm** 虚拟机上退出 ssh 会话，请输入 `exit` 并按 &lt;Enter>。

## <a name="troubleshoot-vpn-issues"></a>排查 VPN 问题

有关如何解决 VPN 错误的信息，请参阅[排查混合 VPN 连接问题](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn)一文。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程系列中创建的资源，请将其删除。

1. 删除计划中声明的资源：

    ```bash
    terraform destroy
    ```

    出现提示时，请输入 `yes` 确认删除资源。

1. 将目录切换到父目录。

    ```bash
    cd ..
    ```

1. 删除 `hub-scope` 目录（包括其中的所有文件）：

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [详细了解如何在 Azure 中使用 Terraform](/azure/terraform)