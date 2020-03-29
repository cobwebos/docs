---
title: 将 Azure 开发人员测试实验室集成到 Azure 管道中
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293209"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>将 Azure 开发人员测试实验室集成到 Azure 管道 CI/CD 管道中

可以使用 Azure*开发人员测试实验室任务*扩展将 Azure 管道持续集成和连续交付 （CI/CD） 生成和发布管道与 Azure DevTest 实验室集成。 扩展安装多个任务，包括： 

- 创建虚拟机 (VM)
- 从 VM 创建自定义映像
- 删除 VM 

例如，这些任务便于快速部署特定测试任务*的黄金映像*VM，然后在测试完成后删除 VM。

本文演示如何使用 Azure DevTest Labs 任务创建和部署 VM、创建自定义映像，然后删除 VM，所有这些都作为一个发布管道。 通常，您将在您自己的自定义生成、测试和部署管道中单独执行任务。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>先决条件

- 注册或登录到[Azure DevOps](https://dev.azure.com)组织，并在组织中[创建项目](/vsts/organizations/projects/create-project)。 
  
- 从可视化工作室应用商店安装 Azure 开发人员测试实验室任务扩展。
  
  1. 转到 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
  1. 选择**获取它免费**。
  1. 从下拉列表中选择 Azure DevOps 组织，然后选择 **"安装**"。 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>创建模板以生成 Azure VM 

本节介绍如何创建用于按需创建 Azure VM 的 Azure 资源管理器模板。

1. 要在订阅中创建资源管理器模板，请按照["使用资源管理器"模板](devtest-lab-use-resource-manager-template.md)中的过程操作。
   
1. 在生成资源管理器模板之前，请在创建 VM 的过程中添加 [WinRM 项目](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)。 部署任务（如*目标计算机上的 Azure 文件副本*和*PowerShell）* 需要 WinRM 访问权限。 WinRM 项目需要主机名作为参数，该参数应为 VM 的完全限定域名 （FQDN）。 
   
   > [!NOTE]
   > 使用具有共享 IP 地址的 WinRM 时，必须添加 NAT 规则，用于将外部端口映射到 WinRM 端口。 如果使用公共 IP 地址创建 VM，则不需要 NAT 规则。
   
   
1. 将模板另存为名为*CreateVMTemplate.json*的文件。
   
1. 将模板签入源代码管理系统。

## <a name="create-a-script-to-get-vm-properties"></a>创建脚本以获取 VM 属性

在发布管道中运行任务步骤（如*Azure 文件副本*或*PowerShell）时*，以下脚本将收集将应用部署到 VM 所需的值。 通常使用这些任务将应用部署到 Azure VM。 这些任务需要 VM 资源组名称、IP 地址和 FQDN 等值。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要创建脚本文件，

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

1. 使用*GetLabVMParams.ps1*等名称保存文件，并将其签入源代码管理系统。 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>在 Azure 管道中创建发布管道

要创建新的发布管道，请进行：

1. 从 Azure DevOps 项目页中，从左侧导航中选择**管道** > **发布**。
1. 选择“新建管道”****。
1. 在 **"选择模板**"下，向下滚动并选择 **"空作业**"，然后选择"**应用**"。

### <a name="add-and-set-variables"></a>添加并设置变量

管道任务使用在 Azure 门户中创建资源管理器模板时分配给 VM 的值。 

要添加值的变量， 

1. 在管道页上，选择 **"变量"** 选项卡。
   
1. 对于每个变量，选择 **"添加"** 并输入名称和值：
   
   |“属性”|“值”|
   |---|---|
   |*vmName*|在资源管理器模板中分配的 VM 名称|
   |*用户*|访问 VM 的用户名|
   |*密码*|用户名的密码。 选择锁定图标以隐藏并保护密码。

### <a name="create-a-devtest-labs-vm"></a>创建开发人员测试实验室 VM

下一步是创建用于未来部署的黄金映像 VM。 通过使用*Azure 开发人员测试实验室创建 VM*任务，在 Azure 开发人员测试实验室实例中创建 VM。

1. 在发布**管道管道**选项卡上，选择**阶段 1**中的超链接文本以查看**阶段任务**，然后选择 **"代理作业**"旁边的**+** 加号。 
   
1. 在 **"添加任务**"下，选择**Azure 开发人员测试实验室创建 VM，** 然后选择"**添加**"。 
   
1. 选择在左侧窗格中**创建 Azure 开发人员测试实验室 VM。** 

1. 在右窗格中填充表单，如下所示：
   
   |字段|“值”|
   |---|---|
   |**Azure RM 订阅**|在下拉列表中从可用**Azure 服务连接**或**可用 Azure 订阅**中选择服务连接或订阅，然后根据需要选择 **"授权**"。<br /><br />**注：** 有关创建到 Azure 订阅的更多受限权限连接的信息，请参阅[Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)。|
   |**实验室名称**|选择将在其中创建实验室 VM 的现有实验室的名称。|
   |**模板名称**|输入保存到源代码存储库的模板文件的完整路径和名称。 您可以使用内置属性来简化路径，例如：<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**模板参数**|输入前面定义的变量的参数：<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**输出变量** > **实验室 VM ID**|输入创建的实验室 VM ID 的变量。 如果使用默认**的 labVMId**，则可以在后续任务中将变量引用为 *$（labVMId）。*<br /><br />您可以创建默认名称以外的名称，但请记住在后续任务中使用正确的名称。 您可以以以下形式编写实验室 VM ID：<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>收集开发人员测试实验室 VM 的详细信息

执行之前创建的脚本，收集开发测试实验室 VM 的详细信息。 

1. 在发布**管道管道**选项卡上，选择**阶段 1**中的超链接文本以查看**阶段任务**，然后选择 **"代理作业**"旁边的**+** 加号。 
   
1. 在 **"添加任务**"下，选择**Azure PowerShell，** 然后选择 **"添加**"。 
   
1. 选择**Azure PowerShell 脚本：** 左侧窗格中的文件路径。 
   
1. 在右窗格中填充表单，如下所示：
   
   |字段|“值”|
   |---|---|
   |**Azure 连接类型**|选择**Azure 资源管理器**。|
   |**Azure 订阅**|选择服务连接或订阅。| 
   |**脚本类型**|选择**脚本文件路径**。|
   |**脚本路径**|输入保存到源代码存储库的 PowerShell 脚本的完整路径和名称。 您可以使用内置属性来简化路径，例如：<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**脚本参数**|输入由上一个任务填充的*labVmId*变量的名称，例如：<br /><br />`-labVmId '$(labVMId)'`|

脚本收集所需的值并将其存储在发布管道中的环境变量中，因此您可以轻松地在后续步骤中引用它们。

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>从开发人员测试实验室 VM 创建 VM 映像

下一个任务是在 Azure DevTest Labs 实例中创建新部署的 VM 映像。 然后，在需要执行开发任务或运行某些测试时，即可随时使用该映像按需创建 VM 的副本。 

1. 在发布**管道管道**选项卡上，选择**阶段 1**中的超链接文本以查看**阶段任务**，然后选择 **"代理作业**"旁边的**+** 加号。 
   
1. 在 **"添加任务**"下，选择**Azure 开发人员测试实验室创建自定义映像**，然后选择"**添加**"。 
   
1. 对任务进行如下配置：
   
   |字段|“值”|
   |---|---|
   |**Azure RM 订阅**|选择服务连接或订阅。|
   |**实验室名称**|选择将在其中创建映像的现有实验室的名称。|
   |**自定义图像名称**|输入自定义图像的名称。|
   |**描述**（可选）|输入说明，以便以后轻松选择正确的图像。|
   |**源实验室 VM** > **源实验室 VM ID**|如果更改 LabVMId 变量的默认名称，请在此处输入它。 默认值为 $(labVMId)。****|
   |**输出变量** > **自定义图像 ID**|如有必要，可以编辑变量的默认名称。|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>将应用部署到开发人员测试实验室 VM（可选）

您可以添加任务以将应用部署到新的 DevTest Labs VM。 通常用于部署应用的任务是在目标计算机上执行*Azure 文件副本*和*PowerShell。*

这些任务的参数所需的 VM 信息存储在发布管道中的三个配置变量中，分别名为**labVmRgName、labVMIp****地址**和**实验室VMFqdn。** 如果只想试验创建开发测试实验室 VM 和自定义映像，而不向其部署应用，可跳过此步骤。

### <a name="delete-the-vm"></a>删除 VM

最后一项任务是删除在 Azure 开发人员测试实验室实例中部署的 VM。 在部署的 VM 上执行所需开发任务或运行所需测试后，通常要删除该 VM。 

1. 在发布**管道管道**选项卡上，选择**阶段 1**中的超链接文本以查看**阶段任务**，然后选择 **"代理作业**"旁边的**+** 加号。 
   
1. 在 **"添加任务**"下，选择**Azure 开发人员测试实验室删除 VM，** 然后选择"**添加**"。 
   
1. 对任务进行如下配置：
   
   - 在**Azure RM 订阅**下，选择服务连接或订阅。 
   - 对于**实验室 VM ID**，如果更改 LabVMId 变量的默认名称，请在此处输入它。 默认值为 $(labVMId)。****
   
### <a name="save-the-release-pipeline"></a>保存发布管道

要保存新版本管道：

1. 在发布管道页上选择"**新建"管道**的名称，然后输入管道的新名称。 
   
1. 选择右上角的 **"保存"** 图标。 

## <a name="create-and-run-a-release"></a>创建并运行版本

要使用新管道创建和运行版本，请：

1. 选择 **"在**发布管道页面上的右上角创建版本"。 
   
1. 在 **"项目"** 下，选择最新生成，然后选择 **"创建**"。
   
1. 在每个发布阶段，刷新 Azure 门户中的 DevTest Labs 实例视图，以查看 VM 创建、映像创建和 VM 删除。

您可以随时使用自定义映像创建 VM。

## <a name="next-steps"></a>后续步骤
- 了解如何[使用 Resource Manager 模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
- 若要深入了解适用于开发测试实验室自动化的资源管理器模板快速入门，请参阅[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
- 如有必要，请参阅[Azure DevOps 故障排除](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)页面。
 
