---
title: "Azure 自动化中的连接资产 | Microsoft Docs"
description: "Azure 自动化中的连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 本文介绍了有关连接的详细信息，以及如何在文本和图形创作中使用连接。"
services: automation
documentationcenter: 
author: bwren
manager: stevenka
editor: tysonn
ms.assetid: f0239017-5c66-4165-8cca-5dcb249b8091
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a4b1092ec4d7f4784e8f421fc633affce7f7185e


---
# <a name="connection-assets-in-azure-automation"></a>Azure 自动化中的连接资产
自动化连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 除 URL 和端口等连接信息外，还包括身份验证所需的信息，如用户名和密码。 使用连接的值将用于连接一个特定应用程序的所有属性保留在一个资产中，而不是创建多个变量。 用户可以从一个位置编辑连接的值，并且你可以在单个参数中将连接名称传递给 Runbook 或 DSC 配置。 可在 Runbook 或 DSC 配置中使用 **Get-AutomationConnection** 活动访问连接的属性。

创建连接时，必须指定“连接类型”。 连接类型是定义了一组属性的模板。 连接为其连接类型中定义的每个属性定义值。 连接类型通过集成模块添加到 Azure 自动化或使用 [Azure 自动化 API](http://msdn.microsoft.com/library/azure/mt163818.aspx) 创建。 在创建连接时，仅能使用安装到你的自动化帐户中的连接类型。

> [!NOTE]
> Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥由主证书加密，并存储在 Azure 自动化中。 在存储安全资产之前，会先使用主证书来解密自动化帐户的密钥，然后使用该密钥来加密资产。
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet
下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化连接。 可在自动化 Runbook 和 DSC 配置中使用的 [Azure PowerShell 模块](../powershell-install-configure.md)已随附了这些 cmdlet。

| Cmdlet | 说明 |
|:--- |:--- |
| [Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx) |检索连接。 包括一个哈希表，其中包括连接的字段的值。 |
| [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) |创建新连接。 |
| [Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx) |删除现有连接。 |
| [Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx) |设置现有连接的一个特定字段的值。 |

## <a name="activities"></a>活动
下表中的活动用于在 Runbook 或 DSC 配置中访问连接。

| 活动 | 说明 |
| --- | --- |
| Get-AutomationConnection |获取要使用的连接。 返回包括该连接属性的哈希表。 |

> [!NOTE]
> 应避免在 **Get- AutomationConnection** 的 -Name 参数中使用变量，因为这可能会使设计时发现 Runbook 或 DSC 配置与连接资产之间的依赖关系变得复杂化。
> 
> 

## <a name="creating-a-new-connection"></a>创建新连接
### <a name="to-create-a-new-connection-with-the-azure-classic-portal"></a>使用 Azure 经典门户创建新连接
1. 在自动化帐户中，单击窗口顶部的“资产”。
2. 在窗口底部，单击“添加设置”。
3. 单击“添加连接”。
4. 在“连接类型”下拉列表中，选择想要创建的连接类型。  向导将显示该特定类型的属性。
5. 完成该向导并单击该复选框以保存新连接。

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>使用 Azure 门户创建新连接
1. 在自动化帐户中，单击“资产”部分以打开“资产”边栏选项卡。
2. 单击“连接”部分以打开“连接”边栏选项卡。
3. 单击边栏选项卡顶部的“添加连接”。
4. 在“类型”下拉列表中，选择想要创建的连接类型。 表单将显示该特定类型的属性。
5. 完成该表单，然后单击“创建”以保存新连接。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 创建新连接
使用 Windows PowerShell 通过 [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) cmdlet 创建新连接。 此 cmdlet 有一个名为 **ConnectionFieldValues** 的参数，预期为一个[哈希表](http://technet.microsoft.com/library/hh847780.aspx)，用于为连接类型定义的每个属性定义值。

下面的示例命令为 [Twilio](http://www.twilio.com) 创建一个新连接，Twilio 是一种可用于发送和接收短信的电话服务。  在[脚本中心](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)中提供了包括 Twilio 连接类型的示例集成模块。  此连接类型为“帐户 SID”和“授权令牌”定义属性，这些是连接到 Twilio 时验证您的帐户时所需的属性。  必须[下载此模块](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)并将其安装到你的自动化帐户中，才能使此示例代码正常工作。

    $AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
    $AuthToken  = "17d4dadfce74153d5853725143c52fd1"
    $FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

    New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 配置中使用连接
请使用 **Get-AutomationConnection** cmdlet 检索 Runbook 或 DSC 配置中的连接。  此活动检索连接中的不同字段的值，并将它们作为[哈希表](http://go.microsoft.com/fwlink/?LinkID=324844)返回，该哈希表随后可用于 Runbook 或 DSC 配置中的相应命令。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例
下面的示例命令演示如何使用前面示例中的 Twilio 连接从 Runbook 中发送短信。  此处使用的 Send-TwilioSMS 活动具有两个参数集，它们分别使用不同方法对 Twilio 服务进行身份验证。  其中一个使用连接对象，而另一个对“帐户 SID”和“授权令牌”使用单个参数。  此示例中显示了这两种方法。

    $Con = Get-AutomationConnection -Name "TwilioConnection"
    $NumTo = "14255551212"
    $NumFrom = "15625551212"
    $Body = "Text from Azure Automation."

    #Send text with connection object.
    Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

    #Send text with connection properties.
    Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken -From $NumFrom -To $NumTo -Body $Body

### <a name="graphical-runbook-samples"></a>图形 Runbook 示例
在图形编辑器的“库”窗格中，右键单击连接，并选择“添加到画布”将 **Get-AutomationConnection** 活动添加到图形 Runbook。

![](media/automation-connections/connection-add-canvas.png)

下图显示了在图形 Runbook 中使用连接的示例。  该示例与上面显示的从文本 runbook 中使用 Twilio 发送短信的示例为同一个示例。  此示例将对使用连接对象向服务进行身份验证的 **Send-TwilioSMS** 活动使用 **UseConnectionObject** 参数集。  此处使用了一个[管道链接](automation-graphical-authoring-intro.md#links-and-workflow)，因为 Connection 参数需要单个对象。

将 PowerShell 表达式而不是常量值用作 **To** 参数中的值，是因为此参数需要一个字符串数组值类型，以便你可以将其发送到多个号码。  PowerShell 表达式允许您提供单个值或一个数组。

![](media/automation-connections/get-connection-object.png)

下图显示的示例与上面显示的示例为同一个示例，但使用 **SpecifyConnectionFields** 参数集，要求单独指定 AccountSid 和 AuthToken 参数，而不使用连接对象进行身份验证。  在这种情况下，指定的是连接的字段，而不是连接本身。  

![](media/automation-connections/get-connection-properties.png)

## <a name="related-articles"></a>相关文章
* [图形创作中的链接](automation-graphical-authoring-intro.md#links-and-workflow)




<!--HONumber=Nov16_HO3-->


