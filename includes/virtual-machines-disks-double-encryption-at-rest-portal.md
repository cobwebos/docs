---
title: 包含文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177000"
---
## <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>入门

1. 登录 [Azure 门户](https://aka.ms/diskencryptionupdates)。

    > [!IMPORTANT]
    > 必须使用提供的[链接](https://aka.ms/diskencryptionupdates)访问 Azure 门户。 双重加密当前在公共 Azure 门户中不可见，不使用链接。

1. 搜索并选择 "**磁盘加密集**"。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。":::

1. 选择“+ 添加”。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="磁盘加密集边栏选项卡的屏幕截图，突出显示了 + Add。":::

1. 选择一个支持的区域。
1. 对于 "**加密类型**"，请选择 "**通过平台托管的密钥和客户托管的密钥进行双重加密**"。

    > [!NOTE]
    > 一旦创建了具有特定加密类型的磁盘加密集，就不能对其进行更改。 如果要使用不同的加密类型，则必须创建新的磁盘加密集。

1. 填写剩余的信息。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="突出显示了 "磁盘加密集创建" 边栏选项卡、区域和对平台管理的密钥进行双重加密的屏幕截图。":::

1. 选择 Azure Key Vault 和密钥，如有必要，请创建新的密钥。

    > [!NOTE]
    > 如果创建 Key Vault 实例，则必须启用软删除和清除保护。 当使用 Key Vault 加密托管磁盘时，这些设置是必需的，并且防止因意外删除而导致数据丢失。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Key Vault 创建 "边栏选项卡的屏幕截图。":::

1. 选择“创建”。
1. 导航到所创建的磁盘加密集，然后选择显示的错误。 这会将磁盘加密设置为 "有效"。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="磁盘加密集显示错误的屏幕截图错误，错误文本为：若要将磁盘、映像或快照与此磁盘加密集相关联，必须向密钥保管库授予权限。":::

    通知应会弹出并成功。 这样做使你能够将磁盘加密集与 Key Vault 协同使用。
    
    ![Key Vault 的成功权限和角色分配的屏幕截图。](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. 导航到磁盘。
1. 选择**加密**。
1. 对于 "**加密类型**"，请选择 "**通过平台托管的密钥和客户托管的密钥进行双重加密**"。
1. 选择磁盘加密集。
1. 选择“保存”。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="托管磁盘的 "加密" 边栏选项卡的屏幕截图，将突出显示上述加密类型。":::

现已在托管磁盘上启用静态加密。
