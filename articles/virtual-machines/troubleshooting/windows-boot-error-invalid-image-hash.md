---
title: Windows 启动管理器错误-0xC0000428 状态无效的映像哈希
titlesuffix: Azure Virtual Machines
description: 本文提供了解决以下问题的步骤：使用了预览映像，并且试用期已过期，这会阻止启动 Azure 虚拟机 (VM) 。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: e58e349d7b7385ec913986c39462c17deadcb61d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969596"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Windows 启动管理器错误-0xC0000428 状态无效的映像哈希

本文提供了解决以下问题的步骤：使用了预览映像，并且试用期已过期，这会阻止启动 Azure 虚拟机 (VM) 。

## <a name="symptom"></a>症状

使用 " [启动诊断](./boot-diagnostics.md) " 查看 VM 的屏幕截图时，你会看到屏幕截图显示 Windows 启动管理器并显示以下消息：

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![图1显示了状态为 "Ox0000428" 的 Windows 启动管理器，以及信息 "Windows 无法验证此文件的数字签名"。](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

或消息：

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![图2显示了状态为 "Ox0000428" 的 Windows 启动管理器和信息 "无法验证此文件的数字签名"。](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>原因

用于构建 VM 的图像是一个预览图像，其中包含过期日期，而不是 RTM (发布到制造) 映像。 

预览图像具有指定的生命周期，并且你在传递到期日期时显示的屏幕截图，这意味着映像的试用已结束。

### <a name="example-of-preview-images"></a>预览图像示例

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

无法延长预览映像的到期日期。 预览到期后，VM 无法再启动。

- 试用期可能会有所不同，具体取决于产品。 例如，Windows 预览映像的试用期为180天。

- 在 Azure 中，作为预览版本的 Windows 的所有映像都将在其说明中包含一条说明，说明它们不适用于生产环境，并且仅可用于指定的试用期或 "预览版本"。

## <a name="solution"></a>解决方案

如果你的映像是预览图像，则无法为使用的映像扩展到期日期，你将需要使用非预览映像 [部署新的 VM](../windows/quick-create-portal.md) 。 以下步骤将帮助你确定是否使用了预览映像，并提供资源来帮助你将数据从该 VM 传输到新的 VM。 如果已将图像确定为预览图像，图像将不可恢复，因为它现在已过期。

根据你的喜好，你可以使用 Azure PowerShell 或 Azure CLI 来查询映像，以确定它是否是预览图像。 你可以使用这些命令来确认图像是预览图像。

### <a name="query-using-azure-powershell"></a>使用 Azure PowerShell 查询

1. 打开 Windows PowerShell 应用程序。
1. 运行以下命令：

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- 在前面的命令中， `<LOCATION>` 将、、 `<PUBLISHER NAME>` `<OFFER NAME>` 和替换 `<YEAR WHEN THIS IMAGE WAS RELEASED>` 为所述的信息。 同时删除 "<" 和 ">" 符号。

  请参阅以下示例：

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>使用 Azure CLI 查询

1. 如果尚未安装，则需要 [安装 Azure CLI](/cli/azure/install-azure-cli)。
1. 下载后，使用命令提示符或 PowerShell 输入 `az login` 命令，然后使用你的帐户凭据登录。
1. 登录后，请输入以下命令：

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- 在前面的命令中， `<LOCATION>` 将、、 `<PUBLISHER NAME>` `<OFFER NAME>` 和替换 `<YEAR WHEN THIS IMAGE WAS RELEASED>` 为所述的信息。 同时删除 "<" 和 ">" 符号。

  请参阅以下示例：

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```