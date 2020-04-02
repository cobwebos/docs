---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6077db0a09b09f7e4bfb859902da53b173845e55
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520765"
---
### <a name="portal"></a>门户

为磁盘设置客户管理的密钥将需要您按特定顺序创建资源（如果您是第一次这样做）。 首先，您需要创建和设置 Azure 密钥保管库。

#### <a name="setting-up-your-azure-key-vault"></a>设置 Azure 密钥保管库

1. 登录到[Azure 门户](https://portal.azure.com/)并搜索密钥保管库
1. 搜索并选择**密钥保管库**。

    [![sse-键-保管库-门户搜索.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure 密钥保管库、磁盘加密集、VM、磁盘和快照必须都位于同一区域中，并且要成功部署。

1. 选择 **"添加"** 以创建新的密钥保管库。
1. 创建新的资源组
1. 输入密钥保管库名称，选择区域，然后选择定价层。
1. 选择 **"审阅 " 创建**"，验证您的选择，然后选择 **"创建**"。

    ![Azure 密钥保管库创建体验的屏幕截图。 显示您创建的特定值](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 密钥保管库完成部署后，选择它。
1. 在 **"设置"** 下选择 **"键**"。
1. 选择 **"生成/导入"**

    !["密钥保管库"资源设置窗格的屏幕截图。 在设置中显示生成/导入按钮。](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 将**密钥类型**设置为**RSA**和**RSA 密钥大小**设置为**2048**。
1. 根据需要填写其余选择，然后选择 **"创建**"。

    ![选择生成/导入按钮后出现的创建键边栏选项卡的屏幕截图](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>设置磁盘加密集

要创建和配置磁盘加密集，必须使用以下链接： https://aka.ms/diskencryptionsets。 如果您位于 Microsoft Azure 政府区域，则必须改用此链接： [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)。 磁盘加密集创建在全局 Azure 门户中尚不可用。

1. 打开适合您所在地区的磁盘加密集链接：

    公共区域：[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 政府区域：[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. 选择 **+添加**。

    ![磁盘加密门户主屏幕的屏幕截图。 突出显示"添加"按钮](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 选择资源组，命名加密集，然后选择与密钥保管库相同的区域。
1. 选择**密钥保管库和密钥**。
1. 选择以前创建的密钥保管库和密钥以及版本。
1. 按“选择”****。
1. 选择 **"审阅 " 创建**然后**创建**。

    ![磁盘加密创建边栏选项卡的屏幕截图。 显示订阅、资源组、磁盘加密集名称、区域和密钥保管库 + 密钥选择器。](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成创建后打开磁盘加密集，并选择弹出的警报。

    ![警报弹出窗口的屏幕截图："要将磁盘、映像或快照与磁盘加密集关联，必须授予密钥保管库的权限"。 选择此警报以继续](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

应弹出两个通知并成功。 这样做将允许您将磁盘加密集与密钥保管库一起使用。

![密钥保管库成功权限和角色分配的屏幕截图。](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

现在，您已经创建并设置了密钥保管库和磁盘加密集，您可以使用加密部署 VM。
VM 部署过程与标准部署过程类似，唯一的区别是您需要将 VM 部署到与其他资源相同的区域中，并且选择使用客户托管密钥。

1. 打开适合您所在地区的磁盘加密集链接：

    公共区域：[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 政府区域：[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. 搜索**虚拟机**并选择 **"添加"** 以创建 VM。
1. 在 **"基本"** 选项卡上，选择与磁盘加密集和 Azure 密钥保管库相同的区域。
1. 根据需要在 **"基本"** 选项卡上填写其他值。

    ![VM 创建体验的屏幕截图，区域值突出显示。](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在 **"磁盘"** 选项卡上，**选择使用客户管理的密钥静态加密**。
1. 在**磁盘加密集**下拉列表中选择磁盘加密集。
1. 根据需要进行其余选择。

    ![VM 创建体验的屏幕截图，磁盘边栏选项卡。 磁盘加密集下拉列表突出显示。](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>在现有磁盘上启用

要管理和配置现有磁盘上的磁盘加密，必须使用以下链接： https://aka.ms/diskencryptionsets。 在全局 Azure 门户中尚未启用现有磁盘上的客户管理密钥。

> [!CAUTION]
> 在连接到 VM 的任何磁盘上启用磁盘加密需要停止 VM。

1. 打开适合您所在地区的磁盘加密集链接：

    公共区域：[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 政府区域：[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. 导航到与磁盘加密集之一位于同一区域的 VM。
1. 打开 VM 并选择 **"停止**"。

    ![示例 VM 的主叠加屏幕截图。 突出显示"停止"按钮](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. 在 VM 完成停止后，选择**磁盘**，然后选择要加密的磁盘。

    ![示例 VM 的屏幕截图，打开磁盘边栏选项卡。 OS 磁盘将突出显示，作为要选择的示例磁盘。](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. 选择 **"加密****"，然后选择使用客户管理的密钥静态加密**，然后在下拉列表中选择磁盘加密集。
1. 选择“保存”。 

    ![示例 OS 磁盘的屏幕截图。 加密边栏选项卡处于打开状态，选择了使用客户管理的密钥进行静态加密，以及示例 Azure 密钥保管库。 进行这些选择后，将选择保存按钮。](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 对要加密的 VM 的任何其他磁盘重复此过程。
1. 当磁盘完成切换到客户管理的密钥后，如果没有要加密的其他附加磁盘，您可以启动 VM。
