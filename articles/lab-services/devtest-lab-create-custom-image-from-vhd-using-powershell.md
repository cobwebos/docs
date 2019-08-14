---
title: 使用 PowerShell 基于 VHD 文件创建 Azure 开发测试实验室自定义映像 | Microsoft 文档
description: 使用 PowerShell 基于 VHD 文件在 Azure 开发测试实验室中自动创建自定义映像
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: b43dc668af74f532838dad3baf1d6e11d51ac69d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964079"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>使用 PowerShell 基于 VHD 文件创建自定义映像

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>分步说明

以下步骤引导完成使用 PowerShell 基于 VHD 文件创建自定义映像：

1. 在 PowerShell 提示符下, 使用对**AzAccount** cmdlet 的以下调用登录到 Azure 帐户。

    ```powershell
    Connect-AzAccount
    ```

1.  通过调用**AzSubscription** cmdlet 来选择所需的 Azure 订阅。 将 **$subscriptionId** 变量的以下占位符替换为有效的 Azure 订阅 ID。

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  通过调用**AzResource** cmdlet 获取实验室对象。 将 **$labRg** 和 **$labName** 变量的以下占位符替换为环境的相应值。

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  将 **$vhdUri** 变量的以下占位符替换为已上传 VHD 文件的 URI。 可以从 Azure 门户中存储帐户的 blob 边栏选项卡获取 VHD 文件的 URI。

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  使用**AzResourceGroupDeployment** cmdlet 创建自定义映像。 将 **$customImageName** 和 **$customImageDescription** 变量的以下占位符替换为对环境有意义的名称。

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>用于基于 VHD 文件创建自定义映像的 PowerShell 脚本

以下 PowerShell 脚本可用于基于 VHD 文件创建自定义映像。 将占位符（以尖括号开始和结束）替换为所需的相应值。

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [复制 Azure 开发测试实验室间的自定义映像](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](devtest-lab-add-vm.md)
