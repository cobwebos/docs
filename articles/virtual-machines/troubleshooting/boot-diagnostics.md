---
title: Azure 中 VM 的启动诊断功能 | Microsoft Docs
description: Azure 中虚拟机的两个调试功能的概述
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 0506527808892bf1ee531d892e2773d095e18560
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362554"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>如何使用启动诊断功能来排查 Azure 中虚拟机的问题

虚拟机进入非可启动状态的原因可能有很多。 若要解决使用资源管理器部署模型创建的虚拟机的问题，可以使用以下调试功能：控制台输出和屏幕截图支持 Azure 虚拟机。 

对于 Linux 虚拟机，可以在门户中查看控制台日志的输出。 对于 Windows 和 Linux 虚拟机，Azure 允许你通过虚拟机监控程序查看 VM 的屏幕截图。 所有区域中的 Azure 虚拟机都支持这两项功能。 请注意，屏幕截图和输出可能需要长达 10 分钟的时间才能显示在存储帐户中。

可以选择“启动诊断”选项以查看日志和屏幕截图。

![资源管理器](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>常见的启动错误

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [找不到操作系统](https://support.microsoft.com/help/4010142)
- [启动故障或 INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>在使用 Azure 门户创建的虚拟机上启用诊断

以下过程适用于使用资源管理器部署模型创建的虚拟机。

在“管理”选项卡上的“监视”部分中，确保“启动诊断”处于打开状态。 从“诊断存储帐户”下拉列表中，选择要将诊断文件放入到的存储帐户。
 
![创建 VM](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> 启动诊断功能不支持高级存储帐户。 如果使用高级存储帐户进行启动诊断，则可能会在启动 VM 时收到 StorageAccountTypeNotSupported 错误。
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>通过 Azure 资源管理器模板部署

若要从 Azure 资源管理器模板进行部署，请导航到虚拟机资源，并追加诊断配置文件部分。 将 API 版本标头设置为“2015-06-15”或更新版本。 最新版本是“2018-10-01”。

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

可以通过诊断配置文件选择要在其中放置这些日志的存储帐户。

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

有关使用模板部署资源的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>在现有的虚拟机上启用启动诊断 

若要在现有的虚拟机上启用启动诊断，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择虚拟机。
2. 在“支持 + 故障排除”部分中选择“启动诊断”，然后选择“设置”选项卡。
3. 在“启动诊断”设置中，将状态更改为“开”，然后从“存储帐户”下拉列表中选择所需的存储帐户。 
4. 保存更改。

    ![更新现有 VM](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

必须重新启动虚拟机才能使更改生效。

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>使用 Azure CLI 启用启动诊断

可以在现有的 Azure 虚拟机上使用 Azure CLI 启用启动诊断。 有关详细信息，请参阅 [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest)。
