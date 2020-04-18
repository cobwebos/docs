---
title: 在 Amazon Web Services 中自动部署 VM
description: 本文演示如何使用 Azure 自动化来自动创建 Amazon Web Service VM
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604832"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure 自动化方案 - 预配 AWS 虚拟机
本文介绍如何利用 Azure 自动化在 Amazon Web Service (AWS) 订阅中预配虚拟机，并为该 VM 提供特定的名称（AWS 称为“标记”VM）。

## <a name="prerequisites"></a>先决条件
您需要具有 Azure 自动化帐户和 Amazon Web 服务 （AWS） 订阅。 有关设置 Azure 自动化帐户和使用 AWS 订阅凭据对其进行配置的详细信息，请参阅[使用 Amazon Web Services 配置身份验证](automation-config-aws-account.md)。 在继续操作之前，应使用 AWS 订阅凭证创建或更新此账户，因为您在以下各节中引用此账户。

## <a name="deploy-amazon-web-services-powershell-module"></a>部署 Amazon Web Services PowerShell 模块
您的 VM 预配运行簿使用 AWS PowerShell 模块来执行其工作。 使用以下步骤将模块添加到配置了 AWS 订阅凭据的自动化帐户。  

1. 打开 Web 浏览器并导航到 [PowerShell 库](https://www.powershellgallery.com/packages/AWSPowerShell/)，并单击“部署到 Azure 自动化”**** 按钮。<br><br> ![AWS PS 模块导入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. 此时将转到 Azure 登录页，进行身份验证后会被路由到 Azure 门户，其中显示了以下页：<br><br> ![“导入模块”页](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 选择要使用的自动化帐户，然后单击"**确定**"以开始部署。

   > [!NOTE]
   > 当 Azure 自动化导入 PowerShell 模块时，它会提取 cmdlet。 在自动化完全导入模块并提取 cmdlet 之前，不会显示这些活动。 此过程可能需要几分钟。  
   > <br>

1. 在 Azure 门户中，打开自动化帐户。
2. 单击"**资产"** 磁贴，在"资产"窗格中选择 **"模块**"。
3. 在“模块”页上，可在列表中看到 AWSPowerShell 模块****。

## <a name="create-aws-deploy-vm-runbook"></a>创建 AWS 部署 VM Runbook
部署 AWS PowerShell 模块后，现在可以创作一个 Runbook，以使用 PowerShell 脚本在 AWS 中自动预配虚拟机。 以下步骤演示如何在 Azure 自动化中使用本机 PowerShell 脚本。  

> [!NOTE]
> 有关其他选项以及此脚本的信息，请访问 [PowerShell 库](https://www.powershellgallery.com/packages/New-AwsVM/)。
> 

1. 通过打开 PowerShell 会话并键入以下命令，从 PowerShell 库下载 PowerShell 脚本 New-AwsVM：<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. 从 Azure 门户中，打开自动化帐户，并在 **"流程自动化**"下选择**Runbook。**  
3. 在“Runbook”页中选择“添加 Runbook”****。
4. 在"添加 Runbook"窗格中，选择 **"快速创建**"以创建新的 Runbook。
5. 在 Runbook 属性窗格中，键入 Runbook 的名称。
6. 从**Runbook 类型**下拉列表中，选择**PowerShell，** 然后单击"**创建**"。<br><br> ![创建 Runbook 窗格](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. 出现“编辑 PowerShell Runbook”页时，请将 PowerShell 脚本复制并粘贴到 Runbook 创作画布中。<br><br> ![Runbook PowerShell 脚本](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > 使用示例 PowerShell 脚本时，请注意以下事项：
    > 
    > * Runbook 包含一些默认参数值。 请评估所有默认值，并根据需要进行更新。
    > * 如果您将 AWS 凭据存储为与`AWScred`名称不同的凭据资产，则需要更新第 57 行的脚本以相应地匹配。  
    > * 在 PowerShell 中使用 AWS CLI 命令（特别是使用此示例 Runbook）时，必须指定 AWS 区域。 否则，cmdlet 会失败。 有关更多详细信息，请查看“用于 PowerShell 的 AWS 工具”文档中的 AWS 主题[指定 AWS 区域](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)。  
    >

7. 若要从 AWS 订阅检索映像名称的列表，请启动 PowerShell ISE 并导入 AWS PowerShell 模块。 通过在 ISE`Get-AutomationPSCredential`环境中替换为`AWScred = Get-Credential`AWS 进行身份验证。 此语句提示您的凭据，您可以提供用户名的访问密钥 ID 和密码的机密访问密钥。 

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
8. 复制并粘贴在 Runbook 中引用的自动化变量中的图像名称之一为`$InstanceType`。 由于在此示例中，您使用的是免费 AWS 分层订阅，因此使用**t2.micro**作为 runbook 示例。  
9. 保存 Runbook，单击“发布”**** 以发布该 Runbook，并在出现提示时单击“是”****。

### <a name="testing-the-aws-vm-runbook"></a>测试 AWS VM Runbook
在继续测试 Runbook 之前，您需要验证以下几点：

* 已创建针对`AWScred`AWS 进行身份验证的资产，或者脚本已更新以引用您的凭据资产名称。    
* AWS PowerShell 模块已在 Azure 自动化中导入。  
* 已创建新的 Runbook，并在必要时验证和更新参数值。  
* 在 Runbook**操作下****记录详细记录**和可选**的日志记录进度记录**已设置为**On**。<br><br> ![Runbook 日志记录和](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)跟踪 。  

1. 单击 **"开始"** 以启动 Runbook，然后在"开始运行簿"窗格打开时单击"**确定**"。
2. 在"开始运行簿"窗格中，提供 VM 名称。 接受在脚本中预配置的其他参数的默认值。 单击“确定”**** 启动 Runbook 作业。<br><br> ![启动 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 为您创建的 Runbook 作业打开作业窗格。 关闭此窗格。
4. 您可以通过从 Runbook 作业窗格中选择 **"所有日志**"来查看作业的进度并查看输出流。<br><br> ![流输出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. 要确认正在预配 VM，请登录到 AWS 管理控制台（如果您当前未登录）。<br><br> ![AWS 控制台部署的 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>后续步骤
* 要开始使用图形运行簿，请参阅[我的第一个图形运行簿](automation-first-runbook-graphical.md)。
* 要开始使用 PowerShell 工作流运行簿，请参阅[我的第一个 PowerShell 工作流运行簿](automation-first-runbook-textual.md)。
* 要了解有关 Runbook 类型及其优点和限制的更多信息，请参阅[Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅[Azure 自动化 中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。