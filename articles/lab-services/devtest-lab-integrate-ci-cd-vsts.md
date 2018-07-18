---
title: 将 Azure 开发测试实验室集成到 VSTS 持续集成和交付管道 | Microsoft Docs
description: 了解如何将 Azure 开发测试实验室集成到 VSTS 持续集成和交付管道
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1af195e644fe93e0c59f5e4402dd8942f5fe1aba
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635500"
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>将 Azure 开发测试实验室集成到 VSTS 持续集成和交付管道
可使用 Visual Studio Team Services (VSTS) 中安装的 *Azure 开发测试实验室任务*扩展，将你的 CI/CD 生成与发布管道与 Azure 开发测试实验室轻松集成。 该扩展将安装三个任务： 
* 创建 VM
* 从 VM 创建自定义映像
* 删除 VM 

通过这一流程，可以轻松完成各种操作，例如为特定测试任务快速部署“黄金映像”，并在测试完成后将其删除。

本文演示如何以完整管道的形式创建和部署 VM、创建自定义映像，然后删除 VM。 通常要在自己的自定义生成-测试-部署管道中单独执行每个任务。

## <a name="before-you-begin"></a>开始之前
必须先从 Visual Studio Marketplace 安装扩展，才能将 CI/CD 管道集成到 Azure 开发测试实验室。
1. 转到 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
1. 选择“安装”。
1. 完成该向导。

## <a name="create-a-resource-manager-template"></a>创建资源管理器模板
本节介绍如何创建用于按需创建 Azure 虚拟机的 Azure 资源管理器模板。

1. 若要在订阅中创建资源管理器模板，请完成[使用资源管理器模板](devtest-lab-use-resource-manager-template.md)中的过程。
1. 在生成资源管理器模板之前，请在创建 VM 的过程中添加 [WinRM 项目](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)。

   需要 WinRM 访问权限才能使用部署任务，例如 *Azure 文件复制*和*目标计算机上的 PowerShell*。

   > [!NOTE]
   > 使用具有共享 IP 地址的 WinRM 时，必须添加 NAT 规则，用于将外部端口映射到 WinRM 端口。 如果创建具有公共 IP 地址的 VM，则不需要执行此步骤。
   >
   >

1. 将模板保存到计算机上的文件。 将该文件命名为 **CreateVMTemplate.json**。
1. 将该模板签入到源代码管理系统。
1. 打开文本编辑器，并在其中粘贴以下脚本。

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. 将该脚本签入到源代码管理系统。 为其命名，例如 **GetLabVMParams.ps1**。

   在发布定义中，在代理上运行此脚本时，如果使用 *Azure 文件复制*或*目标计算机上的 PowerShell* 等任务步骤，则该脚本会收集将应用部署到 VM 所需的值。 这些任务通常用于将应用部署到 Azure VM。 这些任务需要 VM 资源组名称、IP 地址和完全限定的域名 (FDQN) 等值。

## <a name="create-a-release-definition-in-release-management"></a>在 Release Management 中创建发布定义
要创建发布定义，请执行以下操作：

1. 在“生成与发布”中心的“发布”选项卡上，选择加号 (+) 按钮。
2. 在“创建发布定义”窗口中，选择“空”模板，然后选择“下一步”。
3. 选择“稍后选择”和“创建”，以新建有一个默认环境且无链接项目的发布定义。
4. 在新发布定义中，选择环境名称旁边的省略号  (...)，然后选择“配置变量”，可打开快捷菜单。 
5. 在“配置 - 环境”窗口中，对于在发布定义中使用的变量，输入以下值：

   a. 对于 vmName，输入在 Azure 门户中创建资源管理器模板时分配给 VM 的名称。

   b. 对于 userName，输入在 Azure 门户中创建资源管理器模板时分配给 VM 的用户名。

   c. 对于 password，输入在 Azure 门户中创建资源管理器模板时分配给 VM 的密码。 使用“挂锁”图标隐藏和保护密码。

### <a name="create-a-vm"></a>创建 VM

部署的下一阶段是创建 VM，在后续部署中作为“黄金映像”。 使用为此目的专门开发的任务，在 Azure 开发测试实验室实例中创建 VM。 

1. 在发布定义中，选择“添加任务”。
2. 在“部署”选项卡上，添加“Azure 开发测试实验室创建 VM”任务。 对任务进行如下配置：

   > [!NOTE]
   > 若要创建 VM 以用于后续部署，请参阅 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。

   a. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。

   b. 对于“实验室名称”，选择之前创建的实例的名称。

   c. 对于“模板名称”，输入已保存到源代码存储库的模板文件的完整路径和名称。 可使用 Release Management 的内置属性简化路径，例如：

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. 对于“模板参数”，为模板中定义的变量输入参数。 使用在环境中定义的变量的名称，例如：

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. 对于“输出变量 - 实验室 VM ID”，需要输入新创建的 VM 的 ID 以执行后续步骤。 在“输出变量”部分设置自动填充此 ID 的环境变量的默认名称。 可以根据需要编辑变量，但务必记住在后续任务中使用正确的名称。 实验室 VM ID 采用以下形式编写：

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. 执行之前创建的脚本，收集开发测试实验室 VM 的详细信息。 
4. 在发布定义中，选择“添加任务”，然后在“部署选项卡”上添加“Azure PowerShell”任务。 对任务进行如下配置：

   > [!NOTE]
   > 若要收集开发测试实验室 VM 的详细信息，请参阅[部署：Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) 并执行脚本。

   a. 对于“Azure 连接类型”，选择“Azure 资源管理器”。

   b. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表之下选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。

   c. 对于“脚本类型”，选择“脚本文件”。
 
   d. 对于“脚本路径”，输入已保存到源代码存储库的脚本的完整路径和名称。 可使用 Release Management 的内置属性简化路径，例如：
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. 对于“脚本参数”**，输入在上一任务中自动填充了实验室 VM ID 的环境变量的名称，例如： 
      ```
      -labVmId '$(labVMId)'
      ```
    此脚本收集所需的值，并将其存储在发布定义中的环境变量内，因此可在后续步骤中轻松引用它们。

5. 将应用部署到新的开发测试实验室 VM。 用于部署应用的任务通常有 Azure 文件复制和目标计算机上的 PowerShell。
   这些任务参数所需的 VM 相关信息存储在发布定义中名为 **labVmRgName****labVMIpAddress** 和 **labVMFqdn** 的三个配置变量内。 如果只想试验创建开发测试实验室 VM 和自定义映像，而不向其部署应用，可跳过此步骤。

### <a name="create-an-image"></a>创建映像

下一阶段是为 Azure 开发测试实验室实例中新部署的 VM 创建映像。 然后，在需要执行开发任务或运行某些测试时，即可随时使用该映像按需创建 VM 的副本。 

1. 在发布定义中，选择“添加任务”。
2. 在“部署”选项卡上，添加“Azure 开发测试实验室创建自定义映像”任务。 请如下所述对其进行配置：

   > [!NOTE]
   > 若要创建映像，请参阅 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。

   a. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。

   b. 对于“实验室名称”，选择之前创建的实例的名称。

   c. 对于“自定义映像名称”，输入自定义映像的名称。

   d. （可选）对于“说明”，输入说明以便稍后轻松选择适当映像。

   e. 对于“源实验室 VM - 源实验室 VM ID”，如果更改了在之前的任务中自动填充了实验室 VM ID 的环境变量的默认名称，请在此处对其进行编辑。 默认值为 $(labVMId)。

   f. 对于“输出变量 - 自定义映像 ID”，需要管理或删除新创建的映像时，需要输入其 ID。 在“输出变量”部分设置自动填充此 ID 的环境变量的默认名称。 可根据需要编辑该变量。

### <a name="delete-the-vm"></a>删除 VM

最后一个阶段是删除在 Azure 开发测试实验室实例中部署的 VM。 在部署的 VM 上执行所需开发任务或运行所需测试后，通常要删除该 VM。 

1. 在发布定义中，选择“添加任务”，然后在“部署选项卡”上添加“Azure 开发测试实验室删除 VM”任务。 请如下所述对其进行配置：

      > [!NOTE]
      > 若要删除 VM，请参阅 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。

   a. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。
 
   b. 对于“源实验室 VM ID”，如果更改了在之前的任务中自动填充了实验室 VM ID 的环境变量的默认名称，请在此处对其进行编辑。 默认值为 $(labVMId)。

2. 输入发布定义名称，然后保存它。
3. 创建新版本，选择最新生成，并将其部署到定义中的单个环境。

在每个阶段，在 Azure 门户中刷新开发测试实验室实例的视图，可查看创建的 VM 和映像，以及再次删除的 VM。

现可使用自定义映像按需创建 VM。


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 Resource Manager 模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
* 若要深入了解适用于开发测试实验室自动化的资源管理器模板快速入门，请参阅[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
* 如有需要，请参阅 [VSTS 疑难解答](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting)页面。
 
