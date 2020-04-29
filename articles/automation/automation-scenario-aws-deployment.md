---
title: 在 Amazon Web Services 中自动部署 VM
description: 本文演示如何使用 Azure 自动化来自动创建 Amazon Web Service VM
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604832"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure 自动化方案 - 预配 AWS 虚拟机
本文介绍如何利用 Azure 自动化在 Amazon Web Service (AWS) 订阅中预配虚拟机，并为该 VM 提供特定的名称（AWS 称为“标记”VM）。

## <a name="prerequisites"></a>必备条件
需要有一个 Azure 自动化帐户和一个 Amazon Web Services （AWS）订阅。 有关设置 Azure 自动化帐户和使用 AWS 订阅凭据对其进行配置的详细信息，请参阅[使用 Amazon Web Services 配置身份验证](automation-config-aws-account.md)。 在继续操作之前，应用 AWS 订阅凭据创建或更新此帐户，如以下部分中所述。

## <a name="deploy-amazon-web-services-powershell-module"></a>部署 Amazon Web Services PowerShell 模块
VM 预配 runbook 使用 AWS PowerShell 模块来完成工作。 使用以下步骤将模块添加到使用 AWS 订阅凭据配置的自动化帐户。  

1. 打开 Web 浏览器并导航到 [PowerShell 库](https://www.powershellgallery.com/packages/AWSPowerShell/)，并单击“部署到 Azure 自动化”**** 按钮。<br><br> ![AWS PS 模块导入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. 此时将转到 Azure 登录页，进行身份验证后会被路由到 Azure 门户，其中显示了以下页：<br><br> ![“导入模块”页](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 选择要使用的自动化帐户，然后单击 **"确定"** 以开始部署。

   > [!NOTE]
   > 当 Azure Automation 导入 PowerShell 模块时，它将提取 cmdlet。 在 Automation 完全完成导入模块并提取 cmdlet 之前，不会显示这些活动。 此过程可能需要几分钟。  
   > <br>

1. 在 Azure 门户中，打开自动化帐户。
2. 单击 "**资产**" 磁贴，并在 "资产" 窗格中选择 "**模块**"。
3. 在“模块”页上，可在列表中看到 AWSPowerShell 模块****。

## <a name="create-aws-deploy-vm-runbook"></a>创建 AWS 部署 VM Runbook
部署 AWS PowerShell 模块后，现在可以创作一个 Runbook，以使用 PowerShell 脚本在 AWS 中自动预配虚拟机。 下面的步骤演示如何在 Azure 自动化中使用本机 PowerShell 脚本。  

> [!NOTE]
> 有关其他选项以及此脚本的信息，请访问 [PowerShell 库](https://www.powershellgallery.com/packages/New-AwsVM/)。
> 

1. 打开 PowerShell 会话并键入以下命令，从 PowerShell 库下载 PowerShell 脚本 New-awsvm：<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. 在 Azure 门户中，打开自动化帐户，并选择 "**流程自动化**" 下的 " **runbook** "。  
3. 在“Runbook”页中选择“添加 Runbook”****。
4. 在 "添加 runbook" 窗格中，选择 "**快速创建**" 以创建新的 runbook。
5. 在 "Runbook 属性" 窗格中，键入 Runbook 的名称。
6. 从 " **Runbook 类型**" 下拉列表中，选择 " **PowerShell**"，然后单击 "**创建**"。<br><br> ![创建 Runbook 窗格](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. 出现“编辑 PowerShell Runbook”页时，请将 PowerShell 脚本复制并粘贴到 Runbook 创作画布中。<br><br> ![Runbook PowerShell 脚本](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > 使用示例 PowerShell 脚本时，请注意以下事项：
    > 
    > * Runbook 包含一些默认参数值。 请评估所有默认值，并根据需要进行更新。
    > * 如果已将 AWS 凭据存储为名不同于`AWScred`的凭据资产，则需要更新第57行中的脚本以进行相应的匹配。  
    > * 在 PowerShell 中使用 AWS CLI 命令（特别是使用此示例 Runbook）时，必须指定 AWS 区域。 否则，cmdlet 会失败。 有关更多详细信息，请查看“用于 PowerShell 的 AWS 工具”文档中的 AWS 主题[指定 AWS 区域](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)。  
    >

7. 若要从 AWS 订阅检索映像名称的列表，请启动 PowerShell ISE 并导入 AWS PowerShell 模块。 通过在 ISE 环境中`Get-AutomationPSCredential`将替换为， `AWScred = Get-Credential`对 AWS 进行身份验证。 此语句会提示输入凭据，并且可以为用户名和密码的密钥访问密钥提供访问密钥 ID。 

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    返回以下输出：<br><br>
   ![获取 AWS 映像](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. 将 runbook 中引用的其中一个映像名称复制并粘贴为，如中所述`$InstanceType`。 由于在此示例中，你使用的是免费 AWS 分层订阅，因此你可以对 runbook 示例使用**t2。**  
9. 保存 Runbook，单击“发布”**** 以发布该 Runbook，并在出现提示时单击“是”****。

### <a name="testing-the-aws-vm-runbook"></a>测试 AWS VM Runbook
在继续测试 runbook 之前，需要先验证以下各项：

* 已创建了`AWScred`一个用于针对 AWS 进行身份验证的资产，或者已更新该脚本以引用凭据资产名称。    
* AWS PowerShell 模块已导入到 Azure 自动化中。  
* 已创建新的 runbook，并在必要时验证并更新了参数值。  
* 在 runbook 操作**日志记录和跟踪**设置为**On 时**，**记录详细记录**和（可选）记录**进度记录**。<br><br> ![Runbook 日志记录和](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)跟踪。  

1. 单击 "**启动**" 以启动 runbook，然后在 "启动 runbook" 窗格打开时单击 **"确定"** 。
2. 在 "启动 Runbook" 窗格中，提供 VM 名称。 接受您在脚本中预配置的其他参数的默认值。 单击“确定”**** 启动 Runbook 作业。<br><br> ![启动 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 此时会为你创建的 Runbook 作业打开一个“作业”窗格。 关闭此窗格。
4. 通过从 "runbook 作业" 窗格中选择 "**所有日志**"，可以查看作业的进度并查看输出流。<br><br> ![流输出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. 若要确认 VM 是否正在预配，请登录到 AWS 管理控制台（如果当前未登录）。<br><br> ![AWS 控制台部署的 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>后续步骤
* 若要开始处理图形 runbook，请参阅[我的第一个图形 runbook](automation-first-runbook-graphical.md)。
* 若要开始 PowerShell 工作流 runbook，请参阅[我的第一个 PowerShell 工作流 runbook](automation-first-runbook-textual.md)。
* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅[Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。