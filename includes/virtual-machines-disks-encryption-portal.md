---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8d68d2e83bba055e92b99ee9294daf6f2395d8dc
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206288"
---
### <a name="portal"></a>门户

如果你是第一次执行该操作，则为你的磁盘设置客户管理的密钥将要求你按特定顺序创建资源。 首先，需要创建并设置 Azure Key Vault。

#### <a name="setting-up-your-azure-key-vault"></a>设置 Azure Key Vault

1. 登录到[Azure 门户](https://portal.azure.com/)并搜索 Key Vault
1. 搜索并选择 "**密钥保管库**"。

    [![sse-key-vault-portal-search](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure 密钥保管库、磁盘加密集、VM、磁盘和快照必须都位于同一区域和订阅中才能成功部署。

1. 选择 " **+ 添加**" 创建新 Key Vault。
1. 创建新的资源组
1. 输入密钥保管库名称，选择一个区域，然后选择定价层。
1. 依次选择 "查看" 和 "**创建**"，验证你的选择，然后选择 "**创建**"。

    ![Azure Key Vault 创建体验的屏幕截图。 显示您创建的特定值](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 密钥保管库部署完成后，请选择它。
1. 选择 "**设置**" 下的 "**密钥**"。
1. 选择**生成/导入**

    !["Key Vault 资源设置" 窗格的屏幕截图。 显示 "生成/导入" 按钮内的设置。](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 将 "**密钥类型**" 设置为 " **rsa** "，将 " **rsa 密钥大小**" 设置为 " **2080**"。
1. 根据需要填写剩余的选项，然后选择 "**创建**"。

    ![选中 "生成/导入" 按钮后出现的 "创建密钥" 边栏选项卡的屏幕截图](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>设置磁盘加密集

若要创建和配置磁盘加密集，必须使用以下链接： https://aka.ms/diskencryptionsets。 磁盘加密集创建在全局 Azure 门户中尚不可用。

1. 打开 "[磁盘加密集" 链接](https://aka.ms/diskencryptionsets)。
1. 选择“+添加”。

    ![磁盘加密门户主屏幕的屏幕截图。 突出显示 "添加" 按钮](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 选择资源组，将加密集命名为，并选择与密钥保管库相同的区域。
1. 选择 " **key vault 和密钥**"。
1. 选择先前创建的密钥保管库和密钥，以及版本。
1. 按“选择”。
1. 依次选择 "**查看**" 和 **"创建"** 。

    ![磁盘加密创建边栏选项卡的屏幕截图。 显示订阅、资源组、磁盘加密集名称、区域以及密钥保管库 + 键选择器。](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成创建后打开磁盘加密集，并选择弹出的警报。

    ![警报弹出窗口的屏幕截图： ' 若要将磁盘、映像或快照与磁盘加密集相关联，必须向密钥保管库授予权限。 选择此警报以继续](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

应弹出两个通知并成功。 这样做将允许你将磁盘加密集用于密钥保管库。

![密钥保管库的成功权限和角色分配的屏幕截图。](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

现在，你已创建并设置了密钥保管库和磁盘加密集，接下来可以使用加密来部署 VM。
VM 部署过程与标准部署过程类似，唯一的差别在于，你需要将 VM 部署在与其他资源相同的区域中，并选择使用客户托管密钥。

1. 打开 "[磁盘加密集" 链接](https://aka.ms/diskencryptionsets)。
1. 搜索 "**虚拟机**"，并选择 " **+ 添加**" 以创建 VM。
1. 在 "**基本**" 选项卡上，选择与你的磁盘加密集相同的区域，并 Azure Key Vault。
1. 根据需要填写 "**基本**" 选项卡上的其他值。

    ![VM 创建体验的屏幕截图，其中突出显示了 "区域" 值。](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在 "**磁盘**" 选项卡上，选择 "**静态加密"，并使用客户管理的密钥**。
1. 选择 "**磁盘加密集**" 下拉的磁盘加密集。
1. 根据需要进行剩余选择。

    ![VM 创建体验的屏幕截图，"磁盘" 边栏选项卡。 选中 "磁盘加密集" 下拉。](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>在现有磁盘上启用

若要在现有磁盘上管理和配置磁盘加密，必须使用以下链接： https://aka.ms/diskencryptionsets。 在现有磁盘上启用客户管理的密钥目前尚不可用于全局 Azure 门户。

> [!CAUTION]
> 若要在附加到 VM 的任何磁盘上启用磁盘加密，将需要停止 VM。

1. 打开 "[磁盘加密集" 链接](https://aka.ms/diskencryptionsets)。
1. 导航到与你的某个磁盘加密集位于同一区域中的 VM。
1. 打开 VM，并选择 "**停止**"。

    ![示例 VM 的主要覆盖屏幕截图。 突出显示 "停止" 按钮](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. VM 停止后，请选择 "**磁盘**"，然后选择要加密的磁盘。

    ![示例 VM 的屏幕截图，其中打开了 "磁盘" 边栏选项卡。 操作系统磁盘将突出显示，作为你选择的示例磁盘。](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. 选择 "**加密**" 并选择 "静态加密"，并选择 "**使用客户管理的密钥加密**"，然后在下拉列表中选择你的磁盘加密集。
1. 选择“保存”。

    ![示例操作系统磁盘的屏幕截图。 此时会打开 "加密" 边栏选项卡，选择 "使用客户管理的密钥进行静态加密"，并 Azure Key Vault 示例。 进行这些选择后，选择 "保存" 按钮。](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 对于附加到你想要加密的 VM 的任何其他磁盘，请重复此过程。
1. 当磁盘完成切换到客户管理的密钥时，如果没有其他附加磁盘需要加密，则可以启动 VM。
