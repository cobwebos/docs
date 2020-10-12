---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815464"
---
如果你是第一次执行此操作，则为磁盘设置客户托管密钥时将要求你按特定顺序创建资源。 首先，需要创建并设置 Azure Key Vault。

## <a name="set-up-your-azure-key-vault"></a>设置 Azure 密钥保管库

1. 登录到 [Azure 门户](https://aka.ms/diskencryptionupdates)。
1. 搜索并选择“Key Vault”。

    [![展开 "搜索" 对话框时的 Azure 门户屏幕截图。](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure Key Vault、磁盘加密集、VM、磁盘和快照必须都位于同一区域和订阅中才能成功部署。

1. 选择“+添加”以创建新的 Key Vault。
1. 创建新的资源组。
1. 输入 Key Vault 名称，选择区域，然后选择定价层。

    > [!NOTE]
    > 创建 Key Vault 实例时，必须启用软删除和清除保护。 软删除可确保 Key Vault 在给定的保留期（默认为 90 天）内保留已删除的密钥。 清除保护可确保在保留期结束之前，无法永久删除已删除的密钥。 这些设置可防止由于意外删除而丢失数据。 使用 Key Vault 加密托管磁盘时，这些设置是必需的。

1. 选择“审阅 + 创建”，验证选择，然后选择“创建” 。

    ![Azure Key Vault 创建体验的屏幕截图。 显示所创建的特定值](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Key Vault 部署完成后，请选择它。
1. 在“设置”下，选择“密钥” 。
1. 选择“生成/导入”。

    ![Key Vault 资源设置窗格的屏幕截图。 显示设置中的“生成/导入”按钮。](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. 将“密钥类型”设置为“RSA”，将“RSA 密钥大小”设置为“2048”   。
1. 根据需要填写其余选项，然后选择“创建”。

    ![选择“生成/导入”按钮后出现的“创建密钥”边栏选项卡的屏幕截图](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>设置磁盘加密集

1. 搜索“磁盘加密集”并选择它。
1. 在“磁盘加密集”边栏选项卡上，选择“+添加” 。

    ![磁盘加密门户主屏幕的屏幕截图。 突出显示“添加”按钮](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. 选择资源组，命名加密集，然后选择与 Key Vault 相同的区域。
1. 对于 " **加密类型** "，请选择 " **使用客户管理的密钥进行静态加密**"。

    > [!NOTE]
    > 一旦创建了具有特定加密类型的磁盘加密集，就不能对其进行更改。 如果要使用不同的加密类型，则必须创建新的磁盘加密集。

1. 选择 **"单击以选择密钥"**。
1. 选择先前创建的 Key Vault 和密钥，以及版本。
1. 按“选择”。
1. 选择“审阅 + 创建”，然后选择“创建” 。

    ![磁盘加密创建边栏选项卡的屏幕截图。 显示订阅、资源组、磁盘加密集名称、区域以及 Key Vault + 密钥选择器。](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. 完成创建后，打开磁盘加密集，然后选择弹出的警报。

    ![警报弹出窗口的屏幕截图：“若要将磁盘、映像或快照与磁盘加密设置相关联，必须向 Key Vault 授予权限”。 选择此警报以继续](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    应弹出两个通知并成功。 这允许你将磁盘加密集用于密钥保管库。

    ![Key Vault 的成功权限和角色分配的屏幕截图。](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)