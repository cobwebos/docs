---
title: "在 Windows VM 上使用模板的自定义脚本 | Microsoft Docs"
description: "通过将自定义脚本扩展与 Resource Manager 模板配合使用，自动完成 Windows VM 配置任务"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ac63fd346f158d52b359f2d6b398d0d00ea0c67e


---
# <a name="windows-vm-custom-script-extensions-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 模板的 Windows VM 自定义脚本扩展
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## <a name="template-example-for-a-windows-vm"></a>Windows VM 模板示例
在模板的 Resource 节中定义以下资源。

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

在前述示例中，请将文件 URL 和文件名替换为自己的设置。
创作模板之后，可以使用 Azure PowerShell 部署模板。

如果要将脚本 URL 和参数保留为专用，可以将脚本 URL 设置为“专用”。 如果将脚本 URL 设置为“专用”，则只能使用作为受保护设置发送的存储帐户名和密钥来访问它。 对于 1.7 版或更高版本的自定义脚本扩展，也可以受保护设置的形式提供脚本参数。

## <a name="template-example-for-a-windows-vm-with-protected-settings"></a>使用受保护设置的 Windows VM 模板示例
        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
有关最新版自定义脚本扩展的架构信息，请参阅 [Azure Windows VM 扩展配置示例](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

有关使用自定义脚本扩展的 VM 上的应用程序配置的示例，请参阅 [Windows VM 上的自定义脚本扩展](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)。




<!--HONumber=Nov16_HO3-->


