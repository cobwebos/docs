---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 165a115f35810c1bfe463a571affb2e44ed74205
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893713"
---
### <a name="portal"></a>门户

如果你是第一次执行该操作，则为你的磁盘设置客户管理的密钥将要求你按特定顺序创建资源。 首先，需要创建并设置 Azure Key Vault。

#### <a name="setting-up-your-azure-key-vault"></a>设置 Azure Key Vault

1. 登录到[Azure 门户](https://portal.azure.com/)并搜索 Key Vault
1. 搜索并选择 "**密钥保管库**"。

![sse-key-vault-portal-search .png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)

> [!IMPORTANT]
> Azure 密钥保管库、磁盘加密集、VM、磁盘和快照必须都位于同一区域和订阅中才能成功部署。

1. 选择 " **+ 添加**" 创建新 Key Vault。
1. 创建新的资源组
1. 输入密钥保管库名称，选择一个区域，然后选择定价层。
1. 依次选择 "查看" 和 "**创建**"，验证你的选择，然后选择 "**创建**"。

![sse-create-a-key-vault .png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 密钥保管库部署完成后，请选择它。
1. 选择 "**设置**" 下的 "**密钥**"。
1. 选择**生成/导入**

![sse-key-vault-generate-settings .png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 将 "**密钥类型**" 设置为 " **rsa** "，将 " **rsa 密钥大小**" 设置为 " **2080**"。
1. 根据需要填写剩余的选项，然后选择 "**创建**"。

![sse-create-a-key-generate .png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>设置磁盘加密集

若要创建和配置磁盘加密集，必须使用以下链接： https://aka.ms/diskencryptionsets 。 磁盘加密集创建功能在公用 Azure 门户中尚不可用。

1. 打开 "[磁盘加密集" 链接](https://aka.ms/diskencryptionsets)。
1. 选择“+添加”。

![sse-create-disk-encryption-set .png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 选择资源组，将加密集命名为，并选择与密钥保管库相同的区域。
1. 选择 " **key vault 和密钥**"。
1. 选择先前创建的密钥保管库和密钥，以及版本。
1. 按“选择”。
1. 依次选择 "**查看**" 和 **"创建"** 。

![sse-disk-enc-set-blade-key .png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成创建后打开磁盘加密集，并选择弹出的警报。

![sse-disk-enc-alert-fix .png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

应弹出两个通知并成功。 这样做可以将该集用于密钥保管库。

![disk-enc-notification-success .png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

现在，你已创建并设置了密钥保管库和磁盘加密集，接下来可以使用加密来部署 VM。
VM 部署过程与标准部署过程类似，唯一的差别在于，你需要将 VM 部署在与其他资源相同的区域中，并选择使用客户托管密钥。

1. 搜索 "**虚拟机**"，并选择 " **+ 添加**" 以创建 VM。
1. 在 "**基本**" 选项卡上，选择与你的磁盘加密集相同的区域，并 Azure Key Vault。
1. 根据需要填写 "**基本**" 选项卡上的其他值。

![sse-create-a-vm-region .png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在 "**磁盘**" 选项卡上，选择 "静态加密"，并选择 "**客户管理的密钥**"。
1. 选择 "**磁盘加密集**" 下拉的磁盘加密集。
1. 根据需要进行剩余选择。

![sse-create-vm-select-cmk-encryption-set .png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)