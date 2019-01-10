---
title: 结合托管标识使用 Azure 市场映像创建 Terraform Linux 虚拟机
description: 结合托管标识和远程状态管理使用市场映像创建 Terraform Linux 虚拟机，以便轻松地将资源部署到 Azure。
services: terraform
ms.service: terraform
keywords: terraform, devops, MSI, 虚拟机, 远程状态, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: 6c9bef108c2f272c678879124ae2cd4f9ae093ba
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076210"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>结合 Azure 资源的托管标识使用 Azure 市场映像创建 Terraform Linux 虚拟机

本文介绍如何使用 [Terraform 市场映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)来创建装有最新 [Terraform](https://www.terraform.io/intro/index.html) 版本且使用 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)进行配置的 Ubuntu Linux VM (16.04 LTS)。 此映像还会配置远程后端，以使用 Terraform 启用[远程状态](https://www.terraform.io/docs/state/remote.html)管理。 

借助 Terraform 市场映像，可以轻松开始在 Azure 上使用 Terraform，无需手动安装和配置 Terraform。 

不需要对此 Terraform VM 映像进行软件更改。 只需根据预配的虚拟机大小，支付相应的 Azure 硬件使用费。 有关计算费用的详细信息，请参阅 [Linux 虚拟机定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

## <a name="prerequisites"></a>先决条件
在创建 Linux Terraform 虚拟机之前，必须有 Azure 订阅。 如果没有，请参阅[立即创建免费的 Azure 帐户](https://azure.microsoft.com/free/)。  

## <a name="create-your-terraform-virtual-machine"></a>创建 Terraform 虚拟机 

下面是创建 Linux Terraform 虚拟机实例的步骤： 

1. 在 Azure 门户中，转到[创建资源](https://ms.portal.azure.com/#create/hub)列表。

2. 在“在市场中搜索”搜索栏中搜索 **Terraform**。 选择 **Terraform** 模板。 

3. 在 Terraform 详细信息选项卡的右下角选择“创建”按钮。

    ![创建 Terraform 虚拟机](media/terraformmsi.png)

4. 以下部分提供在向导中创建 Terraform Linux 虚拟机所要执行的每个步骤的输入。 以下部分列出了配置每个步骤所需的输入。

## <a name="details-on-the-create-terraform-tab"></a>“创建 Terraform”选项卡中的详细信息

在“创建 Terraform”选项卡中输入以下详细信息：

1. **基础知识**
    
   * **名称**：Terraform 虚拟机的名称。
   * **用户名**：第一个帐户登录 ID。
   * **密码**：第一个帐户密码。 （可以使用 SSH 公钥代替密码。）
   * **订阅**：要在其上创建虚拟机并对其计费的订阅。 必须具有此订阅的资源创建权限。
   * **资源组**：新的或现有的资源组。
   * **位置**：最适合的数据中心。 通常，最合适的数据中心应拥有大部分数据，或者最接近实际位置以实现最快的网络访问。

2. **其他设置**

   * **大小**：虚拟机大小。 
   * **VM 磁盘类型**：SSD 或 HDD。

3. **Terraform 摘要**

   * 验证输入的所有信息是否正确。 

4. **购买**

   * 若要开始预配过程，请选择“购买”。 此时会显示交易条款的链接。 除计算“大小”步骤中选择的服务器大小所产生的费用外，VM 不产生其他任何费用。

Terraform VM 映像执行以下步骤：

* 基于 Ubuntu 16.04 LTS 映像创建具有系统分配标识的 VM。
* 在该 VM 上安装 MSI 扩展，以便能够为 Azure 资源颁发 OAuth 令牌。
* 将 RBAC 权限分配给托管标识，授予资源组的所有者权限。
* 创建 Terraform 模板文件夹 (tfTemplate)。
* 使用 Azure 后端预配置 Terraform 远程状态。

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>访问和配置 Linux Terraform 虚拟机

创建 VM 后，可使用 SSH 登录到该 VM。 使用在步骤 3 的“基本信息”部分中为文本 shell 接口创建的帐户凭据。 可在 Windows 上下载 [Putty](http://www.putty.org/) 之类的 SSH 客户端工具。

使用 SSH 连接到虚拟机后，需要向整个订阅授予对虚拟机上的 Azure 资源的托管标识的参与者权限。 

参与者权限可帮助 VM 上的 MSI 使用 Terraform 在 VM 资源组外部创建资源。 只需运行某个脚本一次，即可轻松完成此操作。 请使用以下命令：

`. ~/tfEnv.sh`

上述脚本使用 [AZ CLI v 2.0 交互式登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in)机制在 Azure 中进行身份验证，并在整个订阅中分配虚拟机托管标识参与者权限。 

 VM 具有 Terraform 远程状态后端。 若要在 Terraform 部署中启用该后端，请将 tfTemplate 目录中的 remoteState.tf 文件复制到 Terraform 脚本所在的根目录。  

 `cp  ~/tfTemplate/remoteState.tf .`

 有关远程状态管理的详细信息，请参阅[有关 Terraform 远程状态的页面](https://www.terraform.io/docs/state/remote.html)。 存储访问密钥在此文件中公开，将 Terraform 配置文件提交到源代码管理之前，需要将存储访问密钥排除在外。

## <a name="next-steps"></a>后续步骤
本文已介绍如何在 Azure 上设置 Terraform Linux 虚拟机。 请参阅以下附加资源，帮助自己详细了解 Azure 上的 Terraform： 

 [Microsoft.com 中的 Terraform 中心](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure 提供程序文档](https://aka.ms/terraform)  
 [Terraform Azure 提供程序源](https://aka.ms/tfgit)  
 [Terraform Azure 模块](https://aka.ms/tfmodules)
 

















