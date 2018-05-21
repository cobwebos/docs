---
title: 使用 CLI 2.0 在 Azure 中捕获 Linux VM 的映像 | Microsoft Docs
description: 使用 Azure CLI 2.0 捕获 Azure VM 的映像以用于批量部署。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: bb70b3ff84392797ce0d93b8cf5d4018ff8ebdd8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>如何创建虚拟机或 VHD 的映像

<!-- generalize, image - extended version of the tutorial-->

若要创建虚拟机 (VM) 的多个副本以用于 Azure 中，需要捕获 VM 或 OS VHD 的映像。 若要创建映像，需要删除个人帐户信息，这使得多次部署更加安全。 执行下列步骤可取消预配现有 VM，解除分配并创建映像。 可以使用此映像，在订阅内的任何资源组中创建 VM。

如果想要创建一份现有 Linux VM 的副本以用于备份或调试，或从本地 VM 上传专用 Linux VHD，请参阅[上传自定义磁盘映像并根据该映像创建 Linux VM](upload-vhd.md)。  

还可使用“Packer”创建自定义配置。 有关使用 Packer 的详细信息，请参阅[如何使用 Packer 在 Azure 中创建 Linux 虚拟机映像](build-image-with-packer.md)。


## <a name="before-you-begin"></a>开始之前
确保符合以下先决条件：

* 需要使用托管磁盘在 Resource Manager 部署模型中创建 Azure VM。 如果尚未创建 Linux VM，则可以使用[门户](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或 [Resource Manager 模板](create-ssh-secured-vm-from-template.md)。 根据需要配置 VM。 例如， [添加数据磁盘](add-disk.md)、应用更新和安装应用程序。 

* 还需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。

## <a name="quick-commands"></a>快速命令

有关本主题的用于测试、评估或了解 Azure 中的 VM 的简化版本，请参阅[使用 CLI 创建 Azure VM 的自定义映像](tutorial-custom-images.md)。


## <a name="step-1-deprovision-the-vm"></a>步骤 1：取消预配 VM
使用 Azure VM 代理取消预配 VM 以删除计算机特定文件和数据。 在源 Linux VM 上，使用带 -deprovision+user 参数的 `waagent` 命令。 有关详细信息，请参阅 [Azure Linux 代理用户指南](../extensions/agent-linux.md)。

1. 使用 SSH 客户端连接到 Linux VM。
2. 在 SSH 窗口中，键入以下命令：
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > 仅在要捕获为映像的 VM 上运行此命令。 它无法保证映像中的所有敏感信息均已清除，或该映像适合再分发。 +user 参数还会删除上次预配的用户帐户。 如果想要在 VM 中保留帐户凭据，只需使用 -deprovision 就地保留用户帐户。
 
3. 键入 **y** 继续。 添加 **-force** 参数即可免除此确认步骤。
4. 该命令完成后，键入 **exit**。 此步骤将关闭 SSH 客户端。

## <a name="step-2-create-vm-image"></a>步骤 2：创建 VM 映像
使用 Azure CLI 2.0 将 VM 标记为通用化并捕获映像。 在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 *myResourceGroup*、*myVnet* 和 *myVM*。

1. 对使用 [az vm deallocate](/cli//azure/vm#deallocate) 取消设置的 VM 解除分配。 以下示例在名为 myResourceGroup 的资源组中释放名为 myVM 的 VM：
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. 使用 [az vm generalize](/cli//azure/vm#generalize) 将 VM 标记为通用化。 以下示例将名为 myResourceGroup 的资源组中名为 myVM 的 VM 标记为通用化：
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. 现在，使用 [az image create](/cli/azure/image#az_image_create) 创建 VM 资源的映像。 以下示例使用名为 myVM 的 VM 资源在名为 myResourceGroup 的资源组中创建名为 myImage 的映像：
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 该映像在与源 VM 相同的资源组中创建。 可以在订阅内的任何资源组中从此映像创建虚拟机。 从管理角度来看，你可能希望为 VM 资源和映像创建特定的资源组。
   >
   > 如果希望将映像存储在具有区域复原能力的存储中，需要在支持[可用性区域](../../availability-zones/az-overview.md)的区域中创建该快照并包括 `--zone-resilient true` 参数。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步骤 3：从捕获的映像创建 VM
使用通过 [az vm create](/cli/azure/vm#az_vm_create) 创建的映像来创建 VM。 以下示例从名为 myImage 的映像创建名为 myVMDeployed 的映像：

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>在另一个资源组中创建 VM 

可在订阅内的任何资源组中根据映像创建 VM。 要在与映像不同的资源组中创建 VM，请指定映像的完整资源 ID。 使用 [az image list](/cli/azure/image#az_image_list) 查看映像列表。 输出类似于以下示例：

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

以下示例使用 [az vm create](/cli/azure/vm#az_vm_create)，通过指定映像资源 ID，在与源映像不同的资源组中创建 VM：

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>步骤 4：验证部署

现在将 SSH 连接到创建的虚拟机以验证部署并开始使用新的 VM。 要通过 SSH 连接，请使用 [az vm show](/cli/azure/vm#az_vm_show) 查找 VM 的 IP 地址或 FQDN：

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>后续步骤
可以从源 VM 映像创建多个 VM。 如果需要对映像进行更改，请执行以下操作： 

- 从映像创建 VM。
- 进行任何更新或配置更改。
- 再次执行相关步骤，对 VM 执行取消预配、解除分配、通用化和创建操作。
- 将此新映像用于将来的部署。 如果需要，请删除原始映像。

有关使用 CLI 管理 VM 的详细信息，请参阅 [Azure CLI 2.0](/cli/azure)。
