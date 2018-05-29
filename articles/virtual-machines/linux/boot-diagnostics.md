---
title: Azure 中 Linux 虚拟机的启用诊断功能 | Microsoft Docs
description: Azure 中 Linux 虚拟机的两个调试功能的概述
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941847"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>如何使用启用诊断功能来解决 Azure 中 Linux 虚拟机的问题

Azure 现在提供对两种调试功能的支持：控制台输出和屏幕截图支持，适用于 Azure 虚拟机 Resource Manager 部署模型。 

将自己的映像加载到 Azure 或者启动某个平台映像时，可能会因为许多原因而导致虚拟机进入无法启动状态。 有了这些功能，就可以轻松进行诊断，将虚拟机从启动故障恢复。

对于 Linux 虚拟机，可以轻松地在门户中查看控制台日志的输出：

![Azure 门户](./media/boot-diagnostics/screenshot1.png)
 
但是，对于 Windows 和 Linux 虚拟机，Azure 也允许从虚拟机监控程序查看 VM 的屏幕截图：

![错误](./media/boot-diagnostics/screenshot2.png)

所有区域的 Azure 虚拟机都支持这两项功能。 请注意，屏幕截图和输出可能需要长达 10 分钟的时间才能显示在存储帐户中。

## <a name="common-boot-errors"></a>常见的启动错误

- [文件系统问题](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [内核问题](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB 错误](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>对新虚拟机启用诊断
1. 从 Azure 门户创建新的虚拟机时，请从部署模型下拉列表中选择“Azure 资源管理器”：
 
    ![资源管理器](./media/boot-diagnostics/screenshot3.jpg)

2. 在“设置”中启用“启动诊断”，然后选择要在其中放置这些诊断文件的存储帐户。
 
    ![创建 VM](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > 启动诊断功能不支持高级存储帐户。 如果使用高级存储帐户进行启动诊断，则可能会在启动 VM 时收到 StorageAccountTypeNotSupported 错误。 
    >
    > 

3. 若要从 Azure 资源管理器模板进行部署，请导航到虚拟机资源，并追加诊断配置文件部分。 记得使用“2015-06-15”API 版本标头。

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. 可以通过诊断配置文件选择要在其中放置这些日志的存储帐户。

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

若要部署启用了启动诊断功能的示例虚拟机，请在此处查看我们的存储库。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>在现有的虚拟机上启用启动诊断 

若要在现有的虚拟机上启用启动诊断，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择虚拟机。
2. 在“支持 + 故障排除”中选择“启动诊断” > “设置”，将状态更改为“启用”，然后选择一个存储帐户。 
4. 确保选中“启动诊断”选项，然后保存所做的更改。

    ![更新现有 VM](./media/boot-diagnostics/enable-for-existing-vm.png)

3. 重新启动 VM，使设置生效。

## <a name="next-steps"></a>后续步骤

如果在使用 VM 启动诊断时出现“未能获取日志内容”错误，请参阅 [VM 启动诊断中的“未能获取日志内容”错误](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur)。