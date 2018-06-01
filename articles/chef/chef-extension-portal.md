---
title: 通过 Azure 门户安装 Chef 客户端
description: 了解如何通过 Azure 门户部署和配置 Chef 客户端
keywords: azure, chef, devops, 客户端, 安装, 门户
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 52f34361d7c1f3dff47f2571a714b8be7764cc6f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259488"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>通过 Azure 门户安装 Chef 客户端
通过 Azure 门户创建或修改 Linux 或 Windows 虚拟机时，可将 Chef 扩展添加到该虚拟机。 本文逐步讲解如何使用新 Linux 虚拟机完成该过程。

## <a name="prerequisites"></a>先决条件
- **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **Chef**：如果没有有效的 Chef 帐户，请注册 [Hosted Chef 免费试用版](https://manage.chef.io/signup)。 若要遵循本文中的说明，需要从 Chef 帐户获取以下值： 
    - organization_validation key
    - rb
    - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>在新 Linux 虚拟机上安装 Chef 扩展
在本部分，我们首先使用 Azure 门户来创建 Linux 虚拟机。 在此过程中，我们还会了解如何在新虚拟机上安装 Chef 扩展。

1. 浏览到 [Azure 门户](http://portal.azure.com)。

1. 在左侧菜单中，选择“虚拟机”选项。 如果“虚拟机”选项未显示，请依次选择“所有服务”、“虚拟机”。

1. 在“虚拟机”选项卡上，选择“添加”。

    ![在 Azure 门户中添加新虚拟机](./media/chef-extension-portal/add-vm.png)

1. 在“计算”选项卡上，选择所需的操作系统。 本演示选择了“Ubuntu Server”。

1. 在“Ubuntu Server”选项卡上，选择“Ubuntu Server 16.04 LTS”。

    ![创建 Ubuntu 虚拟机时，请指定所需的版本](./media/chef-extension-portal/ubuntu-server-version.png)

1. 在“Ubuntu Server 16.04 LTS”选项卡上，选择“创建”。

    ![Ubuntu 提供有关其产品的附加信息](./media/chef-extension-portal/create-vm.png)

1. 在“创建虚拟机”选项卡上，选择“基本信息”。

1. 在“基本信息”选项卡上指定以下值，然后选择“确定”。

    - **名称** - 输入新虚拟机的名称。
    - **VM 磁盘类型** - 指定“SSD”或“HDD”作为存储磁盘类型。 有关 Azure 中虚拟机磁盘类型的详细信息，请参阅 [VM 的高性能高级存储和托管磁盘](/azure/virtual-machines/windows/premium-storage)一文。
    - **用户名** - 输入在虚拟机上被授予了管理员特权的用户名。
    - **身份验证类型** - 选择“密码”。 也可以选择“SSH 公钥”并提供 SSH 公钥值。 本演示（以及屏幕截图中）选择了“密码”。
    - **密码**和**确认密码** - 输入用户的密码。
    - **使用 Azure Active Directory 登录** - 选择“已禁用”。
    - **订阅** - 如果有多个 Azure 订阅，请选择所需的订阅。
    - **资源组** - 输入资源组的名称。
    - **位置** - 选择“美国东部”。

    ![用于创建虚拟机的“基本信息”选项卡](./media/chef-extension-portal/add-vm-basics.png)

1. 在“选择大小”选项卡上选择虚拟机的大小，然后选择“选择”。

1. 在“设置”选项卡上，系统已根据在前面选项卡中选择的值填充了大部分字段的值。 选择“扩展”。

    ![扩展将通过“设置”选项卡添加到虚拟机](./media/chef-extension-portal/add-vm-select-extensions.png)

1. 在“扩展”选项卡上，选择“添加扩展”。

    ![选择“添加扩展”以将扩展添加到虚拟机](./media/chef-extension-portal/add-vm-add-extension.png)

1. 在“新建资源”选项卡上，选择“Linux Chef 扩展(1.2.3)”。

    ![Chef 提供适用于 Linux 和 Windows 虚拟机的扩展](./media/chef-extension-portal/select-linux-chef-extension.png)

1. 在“Linux Chef 扩展”选项卡上，选择“创建”。

1. 在“安装扩展”选项卡上指定以下值，然后选择“确定”。

    - **Chef 服务器 URL** - 输入包含组织名称的 Chef 服务器 URL。 本演示使用了 *https://api.chef.io/organization/hessco*。
    - **Chef 节点名称** - 输入 Chef 节点名称。 这可以是任意值。
    - **运行列表** - 输入已添加到计算机的 Chef 运行列表。 此字段可以留空。
    - **验证客户端名称** - 输入 Chef 验证客户端名称。 本演示使用了 *tarcher-validator*。
    - **验证密钥** - 选择包含启动计算机时使用的验证密钥的文件。 
    - **客户端配置文件** - 选择 Chef 客户端的配置文件。 此字段可以留空。
    - **Chef 客户端版本** - 输入要安装的 Chef 客户端版本。 使用空白值会安装最新版本。 此字段可以留空。
    - **SSL 验证模式** - 选择“无”或“对等”。 本演示选择了“无”。
    - **Chef 环境** - 输入此节点所属的 Chef 环境。 此字段可以留空。
    - **加密数据包机密** - 选择一个文件，其中包含此计算机应有权访问的加密数据包的机密。 此字段可以留空。
    - **Chef 服务器 SSL 证书** - 选择分配给 Chef 服务器的 SSL 证书。 此字段可以留空。

    ![在 Linux 虚拟机上安装 Chef 服务器](./media/chef-extension-portal/install-extension.png)

1. 返回到“扩展”选项卡后，选择“确定”。

1. 返回到“设置”选项卡后，选择“确定”。

1. 返回到“创建”选项卡后（此时会显示已选择和输入的选项的摘要），确认信息和**使用条款**，然后选择“创建”。

使用 Chef 扩展创建和部署虚拟机的过程完成后，会有一条通知指示该操作是成功还是失败。 此外，在创建新虚拟机后，其资源页会在 Azure 门户中自动打开。

![在 Linux 虚拟机上安装 Chef 服务器](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>后续步骤
* [在 Azure 上使用 Chef 创建 Windows 虚拟机](/azure/virtual-machines/windows/chef-automation)