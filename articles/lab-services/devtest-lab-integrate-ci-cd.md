---
title: 将 Azure 开发测试实验室集成到 Azure 管道持续集成和交付管道 | Microsoft Docs
description: 了解如何将 Azure 开发测试实验室集成到 Azure 管道持续集成和交付管道
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
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: 20ba297d22e26aa8c7e20db300173f12582d257e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224471"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>将 Azure 开发测试实验室集成到 Azure Pipelines CI/CD 管道

可以使用*Azure 开发测试实验室任务*扩展将 Azure Pipelines 持续集成和持续交付（CI/CD）生成和发布管道与 Azure 开发测试实验室集成。 此扩展安装多个任务，包括： 

- 创建虚拟机 (VM)
- 从 VM 创建自定义映像
- 删除 VM 

例如，通过这些任务，可以轻松地为特定测试任务快速部署*黄金映像*VM，并在测试完成后删除 vm。

本文介绍如何使用 Azure 开发测试实验室任务来创建和部署 VM，创建自定义映像，然后删除 VM，作为一个发布管道。 通常会在自己的自定义生成、测试和部署管道中单独执行任务。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>先决条件

- 注册或登录到你的[Azure DevOps](https://dev.azure.com)组织，并在组织中[创建一个项目](/vsts/organizations/projects/create-project)。 
  
- 从 Visual Studio Marketplace 安装 Azure 开发测试实验室任务扩展。
  
  1. 转到 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
  1. 选择 "**免费获取**"。
  1. 从下拉列表中选择你的 Azure DevOps 组织，然后选择 "**安装**"。 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>创建模板以构建 Azure VM 

本部分介绍如何创建 Azure 资源管理器模板，该模板可用于按需创建 Azure VM。

1. 若要在订阅中创建资源管理器模板，请按照[使用资源管理器模板](devtest-lab-use-resource-manager-template.md)中的过程进行操作。
   
1. 在生成资源管理器模板之前，请在创建 VM 的过程中添加 [WinRM 项目](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)。 *目标计算机上的* *Azure 文件复制*和 PowerShell 等部署任务需要 WinRM 访问权限。 WinRM 项目需要一个主机名作为参数，该参数应为 VM 的完全限定的域名（FQDN）。 
   
   > [!NOTE]
   > 使用具有共享 IP 地址的 WinRM 时，必须添加 NAT 规则，用于将外部端口映射到 WinRM 端口。 如果创建具有公共 IP 地址的 VM，则不需要使用 NAT 规则。
   
   
1. 将模板作为名为*createvmtemplate.json*的文件保存到计算机。
   
1. 将模板签入到源代码管理系统。

## <a name="create-a-script-to-get-vm-properties"></a>创建脚本以获取 VM 属性

当你在发布管道中的*目标计算机上*运行*Azure 文件复制*或 PowerShell 等任务步骤时，以下脚本将收集你将应用部署到 VM 所需的值。 通常会使用这些任务将应用部署到 Azure VM。 任务需要诸如 VM 资源组名称、IP 地址和 FQDN 之类的值。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

创建脚本文件：

1. 打开文本编辑器，并在其中粘贴以下脚本。
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. 使用类似于*GetLabVMParams*的名称保存该文件，并将其签入到源代码管理系统中。 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>在 Azure 管道中创建发布管道

创建新的发布管道：

1. 在 Azure DevOps 项目页上 **，从左侧导航栏中选择**"**管道** > "。
1. 选择“新建管道”。
1. 在 "**选择模板**" 下，向下滚动并选择 "**空作业**"，然后选择 "**应用**"。

### <a name="add-and-set-variables"></a>添加并设置变量

管道任务使用在 Azure 门户中创建资源管理器模板时分配给 VM 的值。 

添加值的变量： 

1. 在 "管道" 页上，选择 "**变量**" 选项卡。
   
1. 对于每个变量，选择 "**添加**"，然后输入名称和值：
   
   |姓名|ReplTest1|
   |---|---|
   |*vmName*|在资源管理器模板中分配的 VM 名称|
   |*userName*|用于访问 VM 的用户名|
   |*password*|用户名的密码。 选择锁定图标以隐藏并保护密码。

### <a name="create-a-devtest-labs-vm"></a>创建开发测试实验室 VM

下一步是创建黄金映像 VM 以用于将来的部署。 使用*Azure 开发测试实验室创建 vm*任务在 Azure 开发测试实验室实例中创建 vm。

1. 在 "发布管道**管道**" 选项卡上，选择**阶段 1**中的超链接文本以**查看阶段任务**， **+** 然后选择 "**代理作业**" 旁边的加号。 
   
1. 在 "**添加任务**" 下，选择 " **Azure 开发测试实验室创建 VM**"，并选择 "**添加**"。 
   
1. 选择左窗格中的 "**创建 Azure 开发测试实验室 VM** "。 

1. 在右窗格中填充表单，如下所示：
   
   |字段|ReplTest1|
   |---|---|
   |**Azure RM 订阅**|在下拉列表中选择 "**可用 Azure 服务连接**" 或 "**可用 azure 订阅**中的服务连接" 或 "订阅"，并根据需要选择 "**授权**"。<br /><br />**注意：** 有关创建更受限制的权限连接到 Azure 订阅的信息，请参阅[azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)。|
   |**实验室名称**|选择将在其中创建实验室 VM 的现有实验室的名称。|
   |**模板名称**|输入保存到源代码存储库中的模板文件的完整路径和名称。 你可以使用内置属性来简化路径，例如：<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**模板参数**|输入前面定义的变量的参数：<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**输出变量** > **实验室 VM ID**|输入创建的实验室 VM ID 的变量。 如果使用默认的**labVMId**，则可以在后续任务中将变量引用为 *$ （labVMId）* 。<br /><br />你可以创建默认名称以外的名称，但请记住在后续任务中使用正确的名称。 可以采用以下形式编写实验室 VM ID：<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>收集开发测试实验室 VM 的详细信息

执行之前创建的脚本，收集开发测试实验室 VM 的详细信息。 

1. 在 "发布管道**管道**" 选项卡上，选择**阶段 1**中的超链接文本以**查看阶段任务**， **+** 然后选择 "**代理作业**" 旁边的加号。 
   
1. 在 "**添加任务**" 下，选择**Azure PowerShell**，然后选择 "**添加**"。 
   
1. 选择**Azure PowerShell 脚本：左**窗格中的 FilePath。 
   
1. 在右窗格中填充表单，如下所示：
   
   |字段|ReplTest1|
   |---|---|
   |**Azure 连接类型**|选择 " **Azure 资源管理器**"。|
   |**Azure 订阅**|选择服务连接或订阅。| 
   |**脚本类型**|选择 "**脚本文件路径**"。|
   |**脚本路径**|输入已保存到源代码存储库中的 PowerShell 脚本的完整路径和名称。 你可以使用内置属性来简化路径，例如：<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**脚本参数**|输入先前任务填充的*labVmId*变量的名称，例如：<br /><br />`-labVmId '$(labVMId)'`|

此脚本将收集所需值并将其存储在发布管道中的环境变量中，以便可以在后续步骤中轻松引用它们。

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>从开发测试实验室 VM 创建 VM 映像

下一个任务是在 Azure 开发测试实验室实例中创建新部署的 VM 的映像。 然后，在需要执行开发任务或运行某些测试时，即可随时使用该映像按需创建 VM 的副本。 

1. 在 "发布管道**管道**" 选项卡上，选择**阶段 1**中的超链接文本以**查看阶段任务**， **+** 然后选择 "**代理作业**" 旁边的加号。 
   
1. 在 "**添加任务**" 下，选择 " **Azure 开发测试实验室创建自定义映像**"，并选择 "**添加**"。 
   
1. 对任务进行如下配置：
   
   |字段|ReplTest1|
   |---|---|
   |**Azure RM 订阅**|选择服务连接或订阅。|
   |**实验室名称**|选择将在其中创建映像的现有实验室的名称。|
   |**自定义映像名称**|输入自定义映像的名称。|
   |**描述**可有可无|输入说明，以便以后可以轻松地选择正确的映像。|
   |**源实验室 vm** > **源实验室 vm ID**|如果更改了 LabVMId 变量的默认名称，请在此处输入。 默认值为 $(labVMId)。|
   |**输出变量** > **自定义映像 ID**|如果需要，可以编辑变量的默认名称。|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>将应用部署到开发测试实验室 VM （可选）

可以添加任务，将应用部署到新的开发测试实验室 VM。 通常用于部署应用的任务是*Azure 文件复制*和*目标计算机上的 PowerShell*。

这些任务的参数所需的 VM 信息存储在发布管道内名为**labVmRgName**、 **labVMIpAddress**和**labVMFqdn**的三个配置变量中。 如果只想试验创建开发测试实验室 VM 和自定义映像，而不向其部署应用，可跳过此步骤。

### <a name="delete-the-vm"></a>删除 VM

最后一项任务是删除在 Azure 开发测试实验室实例中部署的 VM。 在部署的 VM 上执行所需开发任务或运行所需测试后，通常要删除该 VM。 

1. 在 "发布管道**管道**" 选项卡上，选择**阶段 1**中的超链接文本以**查看阶段任务**， **+** 然后选择 "**代理作业**" 旁边的加号。 
   
1. 在 "**添加任务**" 下，选择 " **Azure 开发测试实验室删除 VM**"，并选择 "**添加**"。 
   
1. 对任务进行如下配置：
   
   - 在 " **AZURE RM 订阅**" 下，选择服务连接或订阅。 
   - 对于 "**实验室 VM ID**"，如果更改了 LabVMId 变量的默认名称，请在此处输入。 默认值为 $(labVMId)。
   
### <a name="save-the-release-pipeline"></a>保存发布管道

若要保存新的发布管道：

1. 在 "发布管道" 页上选择 "**新发布管道**名称"，并输入管道的新名称。 
   
1. 选择右上角的 "**保存**" 图标。 

## <a name="create-and-run-a-release"></a>创建并运行发布

使用新管道创建并运行发布：

1. 在发布管道页面的右上方选择 "**创建发布**"。 
   
1. 在 "**项目**" 下，选择最新版本，然后选择 "**创建**"。
   
1. 在每个发布阶段，刷新 Azure 门户中的开发测试实验室实例的视图，查看创建、创建映像和删除 VM。

你可以根据需要使用自定义映像创建 Vm。

## <a name="next-steps"></a>后续步骤
- 了解如何[使用 Resource Manager 模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
- 若要深入了解适用于开发测试实验室自动化的资源管理器模板快速入门，请参阅[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
- 如有必要，请参阅[Azure DevOps 故障排除](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)页。
 
