---
title: 在 Azure Stack 中提供虚拟机规模集 | Microsoft Docs
description: 了解云操作员如何向 Azure Stack 应用商店中添加虚拟机规模集
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: ''
ms.openlocfilehash: f844576edfb2869ed906415368eb809c7b4b6746
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>在 Azure Stack 中提供虚拟机规模集

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

虚拟机规模集是一种 Azure Stack 计算资源。 可以使用它们部署和管理一组相同的虚拟机。 由于所有虚拟机的配置都相同，因此规模集不需要预配虚拟机。 可以更方便地构建面向大型计算、大数据、容器化工作负荷的大规模服务。

本文将指导你完成在 Azure Stack Marketplace 中提供规模集的过程。 完成此过程之后，用户将可以将虚拟机规模集添加到其订阅。

Azure Stack 上的虚拟机规模集与 Azure 上的虚拟机规模集类似。 有关详细信息，请参阅以下视频：
* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [Guy Bowerman 介绍虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure 堆栈上虚拟机规模集不支持自动缩放。 可以使用 Azure Stack 门户、资源管理器模板或 PowerShell 将更多实例添加到规模集。

## <a name="prerequisites"></a>必备组件
* **Powershell 和工具**

   安装并配置适用于 Azure Stack 的 PowerShell 和 Azure Stack 工具。 请参阅[在 Azure Stack 中启动并运行 PowerShell](azure-stack-powershell-configure-quickstart.md)。

   安装 Azure Stack 工具后，请确保导入以下 PowerShell 模块（路径相对于 AzureStack-Tools-master 文件夹中的 .\ComputeAdmin 文件夹）：

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **操作系统映像**

   如果尚未将操作系统映像添加到 Azure Stack 应用商店，请参阅[将 Windows Server 2016 VM 映像添加到 Azure Stack 应用商店](azure-stack-add-default-image.md)。

   若要支持 Linux，请下载 Ubuntu Server 16.04 并使用带以下参数的 ```Add-AzsVMImage``` 添加它：```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```。


## <a name="add-the-virtual-machine-scale-set"></a>添加虚拟机规模集

针对环境编辑以下 PowerShell 脚本，然后运行该脚本，以便将虚拟机规模集添加到 Azure Stack 应用商店。 

``$User`` 是用于连接管理员门户的帐户。 例如，serviceadmin@contoso.onmicrosoft.com。

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Connect-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="update-images-in-a-virtual-machine-scale-set"></a>更新虚拟机规模集中的映像 
创建虚拟机规模集后，用户可以更新中扩展集，而扩展集，无需重新创建没有映像。 若要更新映像的过程取决于以下方案：

1. 虚拟机规模集的部署模板**指定最新**为*版本*:  

   当*版本*被设置为**最新**中*imageReference*部分的小数位数的模板设置，向上缩放集使用的操作中扩展的最新可用版本适合比例的图像的设置实例。 向上扩展完成后，你可以删除较旧的虚拟机缩放集实例。  (值*发布服务器*，*提供*，和*sku*保持不变)。 

   以下是示例： 指定*最新*:  

          "imageReference": {
             "publisher": "[parameters('osImagePublisher')]",
             "offer": "[parameters('osImageOffer')]",
             "sku": "[parameters('osImageSku')]",
             "version": "latest"
             }

   向上扩展可以使用新的映像之前，你必须下载了新的映像：  

   - 上应用商店的映像时比规模集中的图像的较新版本： 替换较旧的图像将新映像下载。 更换映像后，用户可以继续向上扩展。 

   - 上应用商店的映像版本时与缩放集中的图像相同： 删除正在使用规模集中的映像，然后下载新的映像。 在删除原始图像和新的映像下载之间期间，你无法扩展。 
      
     此过程是需要以 resyndicate 1803 版中引入的稀疏文件格式的映像，使使用。 
 

2. 虚拟机规模集的部署模板**未指定最新**为*版本*并改为指定的版本号：  

     如果你下载的较新版本 （这会更改的可用版本） 的映像，不能扩展规模集。 这是特意设计作为缩放集模板中指定的映像版本必须是可用的不同而不同。  

有关详细信息，请参阅[操作系统磁盘和映像](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images)。  


## <a name="remove-a-virtual-machine-scale-set"></a>删除虚拟机规模集

若要删除虚拟机规模集库项，请运行以下 PowerShell 命令：

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> 库项可能不会立即删除。 可能需要刷新门户几次，该项才会从 Marketplace 中删除。


## <a name="next-steps"></a>后续步骤
[Azure Stack 常见问题解答](azure-stack-faq.md)

