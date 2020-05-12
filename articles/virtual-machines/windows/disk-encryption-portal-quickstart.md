---
title: 使用 Azure 门户创建和加密 Windows VM
description: 本快速入门介绍了如何使用 Azure 门户创建并加密 Windows 虚拟机
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2d1600a688af09515b069a3161d724bb8882596a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791164"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>快速入门：使用 Azure 门户创建和加密 Windows 虚拟机

可以通过 Azure 门户创建 Azure 虚拟机 (VM)。 Azure 门户是基于浏览器的用户界面，用于创建 VM 及其相关资源。 本快速入门介绍如何使用 Azure 门户来部署运行 Ubuntu 18.04 LTS 的 Windows 虚拟机 (VM)、如何创建用于存储加密密钥的密钥保管库，以及如何加密 VM。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。


## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户的左上角，选择“创建资源”  。
1. 在“新建”页的“常用”下，选择“Windows Server 2016 Datacenter”。 
1. 在“基本信息”选项卡中的“项目详细信息”下，确保选择了正确的订阅，然后选择“新建资源组”  。 输入 myResourceGroup 作为名称  。
1. 对于“虚拟机名称”，请输入“MyVM”   。
1. 对于“区域”，请选择在构建密钥保管库时使用的区域（例如“美国东部”）。  
1. 确定“大小”为“标准 D2s v3”。  
1. 在“管理员帐户”下，选择“密码”。   输入用户名和密码。

    :::image type="content" source="../media/disk-encryption/portal-qs-windows-vm-creation.png" alt-text="ResourceGroup 创建屏幕":::

    > [!WARNING]
    > “磁盘”选项卡在“磁盘选项”  下提供了一个“加密类型”字段。 此字段用于指定[托管磁盘](managed-disks-overview.md) + CMK 的加密选项，而不是用于 Azure 磁盘加密。 
    >
    > 为了避免混淆，建议在完成本教程时完全跳过“磁盘”  选项卡。 

1. 选择“管理”选项卡，验证自己是否有一个诊断存储帐户。 如果没有存储帐户，请选择“新建”，为新帐户指定一个名称，然后选择“确定”

    :::image type="content" source="../media/disk-encryption/portal-qs-vm-creation-storage.png" alt-text="ResourceGroup 创建屏幕":::

1. 单击“查看 + 创建”。
1. 在“创建虚拟机”页上，  可以查看要创建的 VM 的详细信息。 准备好以后，选择“创建”  。

部署 VM 需要数分钟。 部署完成后，请转到下一部分。

## <a name="encrypt-the-virtual-machine"></a>加密虚拟机

1. VM 部署完成后，选择“转到资源”。 
1. 在左侧边栏上，选择“磁盘”。 
1. 在“磁盘”屏幕上，选择“加密”  。 

    :::image type="content" source="../media/disk-encryption/portal-qs-disks-to-encryption.png" alt-text="磁盘和加密选择":::

1. 在加密屏幕的“要加密的磁盘”下，选择“OS 和数据磁盘”。  
1. 在“加密设置”下，选择“选择密钥保管库和用于加密的密钥”   。
1. 在“从 Azure Key Vault 选择密钥”屏幕上选择“新建”   。

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="磁盘和加密选择":::

1. 在“创建密钥保管库”屏幕上，确保资源组与用于创建 VM 的资源组相同  。
1. 为密钥保管库命名。  Azure 中的每个密钥保管库都必须具有唯一名称。
1. 在“访问策略”选项卡上，选中“用于卷加密的 Azure 磁盘加密”框   。

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-enable.png" alt-text="磁盘和加密选择":::

1. 选择“查看 + 创建”  。  
1. 在密钥保管库通过验证后，选择“创建”  。 这将让你返回“从 Azure Key Vault 选择密钥”屏幕  。
1. 将“密钥”字段留空，然后选择“选择”   。
1. 在加密屏幕顶部，单击“保存”  。 此时会出现一个弹出窗口，警告你 VM 会重启。 单击 **“是”** 。


## <a name="clean-up-resources"></a>清理资源

当不再需要时，可以删除资源组、虚拟机和所有相关资源。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了一个启用加密密钥的密钥保管库，创建了一个虚拟机，并为虚拟机启用了加密。  

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)
