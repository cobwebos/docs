---
title: 启用静态加密 Azure 门户托管磁盘
description: 使用 Azure 门户为托管磁盘数据启用静态加密。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bf2531536796965f145a9ac3e6a23cbb6634852a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817390"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>使用 Azure 门户为托管磁盘启用静态加密

Azure 磁盘存储支持将静态加密用于托管磁盘。 有关静态加密以及其他托管磁盘加密类型的概念信息，请参阅磁盘加密一文中的 " **静态加密** " 部分：

- 适用于 Linux： [静态加密](./linux/disk-encryption.md#double-encryption-at-rest)
- 适用于 Windows： [静态加密](./windows/disk-encryption.md#double-encryption-at-rest)

## <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>入门

1. 登录 [Azure 门户](https://aka.ms/diskencryptionupdates)。

    > [!IMPORTANT]
    > 必须使用提供的 [链接](https://aka.ms/diskencryptionupdates) 访问 Azure 门户。 双重加密当前在公共 Azure 门户中不可见，不使用链接。

1. 搜索并选择 " **磁盘加密集**"。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。":::

1. 选择“+ 添加”  。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。" **通过平台托管的密钥和客户托管的密钥进行双重加密**"。

    > [!NOTE]
    > 一旦创建了具有特定加密类型的磁盘加密集，就不能对其进行更改。 如果要使用不同的加密类型，则必须创建新的磁盘加密集。

1. 填写剩余的信息。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。":::

1. 选择 Azure Key Vault 和密钥，如有必要，请创建新的密钥。

    > [!NOTE]
    > 如果创建 Key Vault 实例，则必须启用软删除和清除保护。 当使用 Key Vault 加密托管磁盘时，这些设置是必需的，并且防止因意外删除而导致数据丢失。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。":::

1. 选择“创建”  。
1. 导航到所创建的磁盘加密集，然后选择显示的错误。 这会将磁盘加密设置为 "有效"。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。" **通过平台托管的密钥和客户托管的密钥进行双重加密**"。
1. 选择磁盘加密集。
1. 选择“保存”。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="主 Azure 门户的屏幕截图，在搜索栏中突出显示磁盘加密集。":::

现已在托管磁盘上启用静态加密。


## <a name="next-steps"></a>后续步骤

- [Azure PowerShell - 使用客户管理的密钥进行服务器端加密 - 托管磁盘](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [使用服务器端加密启用客户管理的密钥-示例](./linux/disks-enable-customer-managed-keys-cli.md#examples)