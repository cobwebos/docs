---
title: 快速入门 - 在 Azure 门户中创建 Linux VM | Microsoft Docs
description: 本快速入门介绍了如何使用 Azure 门户创建 Linux 虚拟机
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 18ac0291bff2c0fbfffdd5dfa3097f8a6acb561f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Linux 虚拟机

可以通过 Azure 门户创建 Azure 虚拟机 (VM)。 此方法提供基于浏览器的用户界面来创建 VM 及其相关资源。 本快速入门展示了如何使用 Azure 门户在 Azure 中部署运行 Ubuntu 的 Linux 虚拟机 (VM)。 若要查看运行中的 VM，可以通过 SSH 登录到该 VM 并安装 NGINX Web 服务器。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对才能完成本快速入门。 如果有现成的 SSH 密钥对，则可跳过此步骤。

若要创建 SSH 密钥对并登录到 Linux VM，请从 Bash Shell 运行以下命令并根据屏幕上的说明进行操作。 例如，可以使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 或 [Windows Substem for Linux](/windows/wsl/install-win10)。 命令输出包括公钥文件的文件名。 将公钥文件 (`cat ~/.ssh/id_rsa.pub`) 的内容复制到剪贴板：

```bash
ssh-keygen -t rsa -b 2048
```

有关如何创建 SSH 密钥对的更多详细信息，包括 PuTTy 的用法，请参阅[如何将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

## <a name="log-in-to-azure"></a>登录 Azure

在 http://portal.azure.com 登录 Azure 门户

## <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户的左上角选择“创建资源”。

2. 在 Azure Marketplace 资源列表上方的搜索框中，搜索并选择 Canonical 提供的“Ubuntu Server 16.04 LTS”，然后选择“创建”。

3. 提供 VM 名称，例如 *myVM*，将磁盘类型保留为 *SSD*，然后提供用户名，例如 *azureuser*。

4. 。 对于“身份验证类型”，选择“SSH 公钥”，然后将你的公钥粘贴到文本框中。 请务必删除公钥中的所有前导或尾随空格。

    ![在门户边栏选项卡中输入 VM 的基本信息](./media/quick-create-portal/create-vm-portal-basic-blade.png)

5. 选择“新建”资源组，然后提供一个名称，例如 *myResourceGroup*。 选择所需**位置**，然后选择“确定”。

4. 为 VM 选择大小。 例如，可以按*计算类型*或*磁盘类型*进行筛选。 建议的 VM 大小是 *D2s_v3*。

    ![显示 VM 大小的屏幕截图](./media/quick-create-portal/create-linux-vm-portal-sizes.png)

5. 在“设置”下，保留所有默认设置，然后选择“确定”。

6. 在“摘要”页上，选择“创建”以启动 VM 部署。

7. VM 将固定到 Azure 门户仪表板。 完成部署后，会自动打开 VM 摘要。

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

创建与 VM 的 SSH 连接。

1. 选择 VM 的概述页面上的“连接”按钮。 

    ![门户 9](./media/quick-create-portal/portal-quick-start-9.png)

2. 在“连接到虚拟机”页面中，保留默认选项，以使用 DNS 名称通过端口 22 进行连接。 在“使用 VM 本地帐户登录”中，将显示一个连接命令。 单击相应按钮来复制该命令。 下面的示例展示了 SSH 连接命令的样式：

    ```bash
    ssh azureuser@myvm-123abc.eastus.cloudapp.azure.com
    ```

3. 将 SSH 连接命令粘贴到 Windows 上的某个 Shell（例如 Azure Cloud Shell 或 Ubuntu 上的 Bash）中来创建连接。 

## <a name="install-web-server"></a>安装 Web 服务器

若要查看运行中的 VM，请安装 NGINX Web 服务器。 若要更新包源并安装最新的 NGINX 包，请从 SSH 会话运行以下命令：

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成后，`exit` SSH 会话，返回 Azure 门户中的 VM 属性。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

网络安全组 (NSG) 保护入站和出站流量的安全。 从 Azure 门户创建 VM 后，会在进行 SSH 连接的端口 22 上创建入站规则。 由于此 VM 托管着 Web 服务器，因此需要为端口 80 创建 NSG 规则。

1. 在 VM 概述页中，选择“网络”。
2. 此时将显示现有的入站和出站规则的列表。 选择“添加入站端口规则”。
3. 选择顶部的“基本”选项，然后从可用服务列表中选择“HTTP”。 将为你提供端口 80、优先级和名称。
4. 若要创建规则，请选择“添加”。

## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

安装 NGINX 并向 VM 打开端口 80 以后，即可通过 Internet 访问 Web 服务器。 打开 Web 浏览器，输入 VM 的公用 IP 地址。 可以在 VM 概述页面上或者在添加入站端口规则的“网络”页面顶部找到公用 IP 地址。

![NGINX 默认站点](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一台简单的虚拟机、一条网络安全组规则组和规则，并安装了一台基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。

> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)
