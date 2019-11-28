---
title: 教程 - 使用 Terraform 从 Azure 市场映像创建具有托管标识的 Linux VM
description: 使用 Azure 市场映像创建具有托管标识和远程状态管理的 Terraform Linux VM
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 6bc6bec2a13dcd5747823de739d1dd11c6027091
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158969"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>教程：使用 Terraform 从 Azure 市场映像创建具有托管标识的 Linux VM

本文介绍如何使用 [Terraform 市场映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)来创建装有最新 [Terraform](https://www.terraform.io/intro/index.html) 版本且使用 [Azure 资源的托管标识](/azure/active-directory/managed-service-identity/overview)进行配置的 Ubuntu Linux VM (16.04 LTS)。 此映像还会配置远程后端，以使用 Terraform 启用[远程状态](https://www.terraform.io/docs/state/remote.html)管理。 

借助 Terraform 市场映像，可以轻松开始在 Azure 上使用 Terraform，无需手动安装和配置 Terraform。 

不需要对此 Terraform VM 映像进行软件更改。 仅需根据预配的 VM 大小支付 Azure 硬件使用费用。 

有关计算费用的详细信息，请参阅 [Linux VM 定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

## <a name="prerequisites"></a>先决条件
在创建 Linux Terraform VM 之前，必须有 Azure 订阅。 如果没有，请参阅[立即创建免费的 Azure 帐户](https://azure.microsoft.com/free/)。  

## <a name="create-your-terraform-vm"></a>创建 Terraform VM 

下面是创建 Linux Terraform VM 实例的步骤： 

1. 在 Azure 门户中，转到[创建资源](https://ms.portal.azure.com/#create/hub)列表。

1. 在“在市场中搜索”  搜索栏中搜索 **Terraform**。 

1. 选择“创建”  。 

1. 以下部分提供在向导中创建 Terraform Linux VM 所要执行的每个步骤的输入。 以下部分列出了配置每个步骤所需的输入。

## <a name="details-on-the-create-terraform-tab"></a>“创建 Terraform”选项卡中的详细信息

在“创建 Terraform”选项卡中输入以下详细信息： 

1. **基础知识**
    
   * **名称**：Terraform VM 的名称。
   * **用户名**：第一个帐户登录 ID。
   * **密码**：第一个帐户密码。 （可以使用 SSH 公钥代替密码。）
   * **订阅**：要在其上创建虚拟机并对其计费的订阅。 必须具有此订阅的资源创建权限。
   * **资源组**：新的或现有的资源组。
   * **位置**：最适合的数据中心。 通常，最合适的数据中心应拥有大部分数据，或者最接近实际位置以实现最快的网络访问。

2. **其他设置**

   * **大小**：VM 的大小 
   * **VM 磁盘类型**：SSD 或 HDD。

3. **Terraform 摘要**

   * 验证输入的所有信息是否正确。 

4. **购买**

   * 若要开始预配过程，请选择“购买”。  此时会显示交易条款的链接。 除计算“大小”步骤中选择的服务器大小所产生的费用外，VM 不产生其他任何费用。

Terraform VM 映像执行以下步骤：

* 基于 Ubuntu 16.04 LTS 映像创建具有系统分配标识的 VM。
* 在 VM 上安装 Azure 资源扩展的托管标识，以便能够为 Azure 资源颁发 OAuth 标记。
* 将 RBAC 权限分配给托管标识，授予资源组的所有者权限。
* 创建 Terraform 模板文件夹 (tfTemplate)。
* 使用 Azure 后端预配置 Terraform 远程状态。

## <a name="access-and-configure-a-linux-terraform-vm"></a>访问和配置 Linux Terraform VM

创建 VM 后，请执行以下步骤：

1. 使用 SSH 登录 VM。 使用你在上一部分中创建的帐户凭据。 可在 Windows 上下载 [Putty](https://www.putty.org/) 之类的 SSH 客户端工具。

1. 向 VM 上的 Azure 资源托管标识授予整个订阅的参与者权限。 

    参与者权限有助于 VM 上的 Azure 资源托管标识使用 Terraform 在 VM 资源组外部创建资源。 通过运行以下脚本来执行此操作： 
    
    ```bash
    . ~/tfEnv.sh
    ```

    该脚本使用 [Azure CLI 交互式登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively)机制对 Azure 进行身份验证。 此进程为整个订阅分配[托管标识参与者权限](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)。 

1. VM 具有 Terraform 远程状态后端。 若要在 Terraform 部署中启用该后端，必须将 `remoteState.tf` 文件复制到 Terraform 脚本所在的根目录。

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    有关远程状态管理的详细信息，请参阅 [Terraform 远程状态](https://www.terraform.io/docs/state/remote.html)。 此文件中公开了存储访问密钥。 在将 Terraform 配置文件提交到源代码管理之前将其排除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [详细了解如何在 Azure 中使用 Terraform](/azure/terraform)