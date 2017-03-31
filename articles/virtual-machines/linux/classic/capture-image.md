---
title: "捕获 Linux VM 的映像 | Microsoft Docs"
description: "了解如何使用经典部署模型捕获基于 Linux 的 Azure 虚拟机 (VM) 的映像。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 4f4013225e3ea7afb34628bab47ec3b1432bb2b2
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>How to capture a classic Linux virtual machine as an image（如何捕获用作映像的经典 Linux 虚拟机）
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 了解如何[使用 Resource Manager 模型执行这些步骤](../../virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

本文将演示如何捕获运行 Linux 的经典 Azure 虚拟机 (VM) 作为创建其他虚拟机的映像。 此映像包括操作系统磁盘和附加到 VM 的数据磁盘。 它不包括网络配置，因此在使用此映像创建其他 VM 时需要进行网络配置。

Azure 在“映像”下存储映像，以及任何已上载的映像。 有关映像的详细信息，请参阅[关于 Azure 中的虚拟机映像][About Virtual Machine Images in Azure]。

## <a name="before-you-begin"></a>开始之前
这些步骤假定已使用经典部署模型创建了 Azure VM 并配置了操作系统，包括附加任何数据磁盘。 如果需要创建 VM，请阅读[如何创建 Linux 虚拟机][How to Create a Linux Virtual Machine]。

## <a name="capture-the-virtual-machine"></a>捕获虚拟机
1. 使用所选 SSH 客户端[连接到 VM](../../virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
2. 在 SSH 窗口中，键入以下命令。 `waagent` 的输出结果可能会因此实用程序的版本而略有差异：

    ```bash
    sudo waagent -deprovision+user
    ```

    上述命令将尝试清除系统并使其适合重新预配。 此操作执行以下任务：

   * 删除 SSH 主机密钥（如果在配置文件中 Provisioning.RegenerateSshHostKeyPair 为“y”）
   * 清除 /etc/resolv.conf 中的 nameserver 配置
   * 从 /etc/shadow 中删除 `root` 用户的密码（如果在配置文件中 Provisioning.DeleteRootPassword 为“y”）
   * 删除缓存的 DHCP 客户端租赁
   * 将主机名重置为 localhost.localdomain
   * 删除上次预配的用户帐户（从 /var/lib/waagent 获得）**和关联的数据**。

     > [!NOTE]
     > 取消预配会删除文件和数据，目的是使映像“通用化”。 仅在想要捕获为新映像模板的 VM 上运行此命令。 无法确保映像中的所有敏感信息均已清除，或者说无法确保该映像适合再分发给第三方。

3. 键入 **y** 继续。 添加 `-force` 参数即可免除此确认步骤。
4. 键入 **Exit** 关闭 SSH 客户端。

   > [!NOTE]
   > 剩余步骤假定已在客户端计算机上[安装 Azure CLI](../../../cli-install-nodejs.md)。 以下所有步骤也可以在 [Azure 经典门户][Azure classic portal]中执行。

5. 从客户端计算机中打开 Azure CLI 并登录到你的 Azure 订阅。 有关详细信息，请阅读[从 Azure CLI 连接到 Azure 订阅](../../../xplat-cli-connect.md)。
6. 请确保你是在服务管理模式下：

    ```azurecli
    azure config mode asm
    ```

7. 关闭已取消预配的 VM。 以下示例关闭名为 `myVM` 的 VM：

    ```azurecli
    azure vm shutdown myVM
    ```

   > [!NOTE]
   > 可以使用 `azure vm list` 查看在订阅中创建的所有 VM 的列表

8. 停止 VM 后，捕获映像。 以下示例捕获名为 `myVM` 的 VM，并创建名为 `myNewVM` 的通用映像：

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` 子命令将删除原始虚拟机。

9. 新映像现在会出现在映像列表中，可以用于配置任何新的 VM。 你可以使用以下命令来查看它：

   ```azurecli
   azure vm image list
   ```

   在 [Azure 门户](http://portal.azure.com)中，新映像会出现在属于“计算”服务的“VM 映像(经典)”中。 可通过在 Azure 服务列表底部单击“更多服务”，然后在“计算”服务中查找来访问“VM 映像(经典)”。   

   ![成功捕获映像](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>后续步骤
该映像已就绪，可用于创建 VM。 可以使用 Azure CLI 命令 `azure vm create` 并提供所创建的映像名称。 有关详细信息，请参阅[将 Azure CLI 与经典部署模型配合使用](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。

此外，也可以使用 [Azure 经典门户][Azure classic portal]创建自定义 VM，方法是使用**从库中**方法并选择所创建的映像。 有关详细信息，请参阅[如何创建自定义 VM][How to Create a Custom Virtual Machine]。

**另请参阅：**[Azure Linux 代理用户指南](../../virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure classic portal]:http://manage.windowsazure.com
[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk.md
[How to Create a Linux Virtual Machine]:create-custom.md

