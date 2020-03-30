---
title: 我在 Azure 自动化中的第一个图形 Runbook
description: 本教程指导完成创建、 测试和发布一个简单图形 Runbook。
keywords: runbook, runbook 模板, runbook 自动化, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6bd360b2075c337e3ed3d69d84368d16571a9335
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536048"
---
# <a name="my-first-graphical-runbook"></a>我的第一个图形 Runbook

> [!div class="op_single_selector"]
> * [图形](automation-first-runbook-graphical.md)
> * [电源外壳](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

本教程指导在 Azure 自动化中创建 [图形 Runbook](automation-runbook-types.md#graphical-runbooks)。 从可以测试和发布的简单 Runbook 开始，同时了解如何跟踪 Runbook 作业的状态。 然后修改 Runbook 以实际管理 Azure 资源，在这种情况下启动 Azure 虚拟机。 通过添加 Runbook 参数和条件链接，完成本教程以使 Runbook 更加健壮。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果您还没有，您可以[激活您的 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于您停止并启动此计算机，因此它不应是生产 VM。

## <a name="step-1---create-runbook"></a>步骤 1 - 创建 Runbook

首先创建一个简单的运行手册，输出文本`Hello World`。

1. 在 Azure 门户中，打开自动化帐户。 

    通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 这些资产大多数是自动包含在新自动化帐户中的模块。 您还应将凭据资产与您的订阅相关联。
2. 在 **"流程自动化**"下选择**Runbook**以打开 Runbook 列表。
3. 通过选择 **"创建运行簿"创建新的 Runbook。**
4. 将该 Runbook 命名为 **MyFirstRunbook-Graphical**。
5. 在这种情况下，您将创建一个[图形运行簿](automation-graphical-authoring-intro.md)。 为**Runbook 类型**选择**图形**。<br> ![新建 Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. 单击“创建”以创建 Runbook 并打开图形编辑器 **** 。

## <a name="step-2---add-activities"></a>步骤 2 - 添加活动

编辑器左侧的库控件允许选择要添加到 Runbook 的活动。 您将添加 cmdlet`Write-Output`以从 Runbook 输出文本。

1. 在"库"控件中，单击搜索字段并键入`write-output`。 搜索结果显示在下图中。 <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 向下滚动到列表的底部。 右键单击**写入输出**并选择"**添加到画布**"。 或者，您可以单击 cmdlet 名称旁边的省略号 （...），然后选择"**添加到画布**"。
1. 单击画布上的 **Write-Output** 活动。 此操作会打开可用于配置活动的“配置控件”页。
1. "**标签"** 字段默认为 cmdlet 的名称，但您可以将其更改为更友好的内容。 将其更改为`Write Hello World to output`。
1. 单击“参数”为 cmdlet 参数提供值****。

   某些 cmdlet 具有多个参数集，您需要选择要使用的参数集。 在这种情况下，`Write-Output`只有一个参数集。

1. 选择参数`InputObject`。 这是用于指定要发送到输出流的文本的参数。
1. **数据源**下拉菜单提供可用于填充参数值的源。 在此菜单中，选择**PowerShell 表达式**。 

   您可以将来自此类源的输出用作其他活动、自动化资产或 PowerShell 表达式。 在这种情况下，输出只是`Hello World`。 可以使用 PowerShell 表达式并指定一个字符串。<br>

1. 在 **"表达式"** 字段中，`Hello World`键入然后单击 **"确定**"两次以返回到画布。
1. 通过单击“保存” **** 保存 Runbook。

## <a name="step-3---test-the-runbook"></a>步骤 3 - 测试 Runbook

在发布 Runbook 使其在生产中可用之前，应对其进行测试以确保其正常工作。 测试 Runbook 会运行其草稿版本，并允许您以交互方式查看其输出。

1. 选择 **"测试"窗格**以打开"测试"窗格。
1. 单击“启动”以启动测试 **** 。 这应该是唯一的已启用选项。
1. 请注意，将创建[Runbook 作业](automation-runbook-execution.md)，其状态将显示在窗格中。

   作业状态以 开始`Queued`，指示作业正在等待云中的 Runbook 工作人员变为可用。 状态更改为`Starting`工作人员声明作业时的状态。 最后，当 Runbook 实际开始运行时，状态变为`Running`该状态。

1. 运行簿作业完成后，"测试"窗格将显示其输出。 在这种情况下，您将看到`Hello World`。

    ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

您创建的 Runbook 仍处于草稿模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 选择“发布”以发布该 Runbook，并在出现提示时选择“是”********。
1. 向左滚动以查看 Runbook 页上的 Runbook，并注意 **"创作状态"** 值设置为 **"已发布**"。
1. 往回向右滚动查看“MyFirstRunbook-Graphical”页****。

   顶部的选项允许您立即启动 Runbook、安排将来的开始时间或创建[Webhook，](automation-webhooks.md)以便通过 HTTP 调用启动 Runbook。

1. 选择“启动”，并在出现提示时选择“是”******** 启动 runbook。
1. 为已创建的 Runbook 作业打开作业窗格。 验证**作业状态**字段是否显示 **"已完成**"。
1. 单击 **"输出**"以打开"输出"页，您可以在`Hello World`其中显示。
1. 关闭“输出”页。
1. 单击“所有日志”打开 Runbook 作业的“流”窗格****。 您应该只在输出`Hello World`流中看到。 

    请注意，如果 Runbook 写入 Runbook，则"流"窗格可以显示 Runbook 作业的其他流，如"详细"和"错误"流。
1. 关闭"流"窗格和作业窗格以返回到 **"MyFirstRunbook-图形"** 页。
1. 要查看 Runbook 的所有作业，请在 **"资源**"下选择**作业**。 "作业"页列出了 Runbook 创建的所有作业。 您应该只看到一个列出的作业，因为您只运行该作业一次。
1. 单击作业名称可打开启动 Runbook 时查看的相同作业窗格。 使用此窗格可以查看为 Runbook 创建的任何作业的详细信息。

## <a name="step-5---create-variable-assets"></a>步骤 5 - 创建变量资产

您已经测试并发布了 Runbook，但到目前为止，它没有任何有用的管理 Azure 资源。 在配置 Runbook 进行身份验证之前，必须创建一个变量来保存订阅 ID，设置要进行身份验证的活动，然后引用该变量。 通过包含对订阅上下文的引用，可以轻松地处理多个订阅。

1. 从导航窗格上的 **"订阅"** 选项复制订阅 ID。
1. 在"自动化帐户"页中，选择 **"共享资源**"下的**变量**。
1. 选择“添加变量”****。
1. 在"新建变量"页上，在提供的字段中进行以下设置。

    * **名称**--`AzureSubscriptionId`输入 。
    * **值**-- 输入订阅 ID。 
    * **类型**-- 保持字符串选择。
    * **加密**-- 使用默认值。
1. 单击“创建”以创建该变量****。

## <a name="step-6---add-authentication"></a>步骤 6 - 添加身份验证

现在，您拥有一个变量来保存订阅 ID，则可以将 Runbook 配置为使用订阅的"运行为"凭据进行身份验证。 为此，将 Azure 运行为连接添加为资产。 您还必须将[连接-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) cmdlet 和[Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) cmdlet 添加到画布中。

>[!NOTE]
>对于 PowerShell 运行`Add-AzAccount`簿`Add-AzureRMAccount`，并且是`Connect-AzAccount`的别名。 请注意，这些别名不适用于图形运行簿。 图形运行簿只能使用`Connect-AzAccount`自身。

1. 导航到 Runbook，并在**MyFirstRunbook-图形**页面上选择 **"编辑**"。
1. 您不再需要该`Write Hello World to output`条目了。 只需单击省略号并选择 **"删除**"。
1. 在库控件中，展开**ASSETS**，然后**展开连接**。 `AzureRunAsConnection`通过选择 **"添加到画布"添加到画布**。
1. 将 `AzureRunAsConnection` 重命名为 `Get Run As Connection`。
1. 在"库"控件中`Connect-AzAccount`，在搜索字段中键入。
1. 添加到`Connect-AzAccount`画布。
1. 悬停，`Get Run As Connection`直到形状底部出现一个圆圈。 单击圆圈并拖动箭头以`Connect-AzAccount`形成链接。 Runbook 从`Get Run As Connection`开始，然后`Connect-AzAccount`运行 。<br> ![创建活动之间的链接](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 在画布上，选择`Connect-AzAccount`。 在"配置"控件窗格中，在 **"标签"** 字段中键入 **"登录到 Azure"。**
1. 单击**参数**，将显示"活动参数配置"页。
1. `Connect-AzAccount` cmdlet 具有多个参数集，您需要在提供参数值之前选择一个参数集。 单击**参数集**，然后选择**具有订阅 ID 的服务主体证书**。
1. 此参数集的参数显示在"活动参数配置"页上。 单击“APPLICATIONID”****。<br> ![添加 Azure 帐户参数](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 在"参数值"页上，进行以下设置，然后单击"**确定**"。

   * **数据源**-- 选择**活动输出**。
   * 数据源列表 - 选择**获取自动化连接**。
   * **字段路径**-`ApplicationId`类型 。 您正在指定字段路径的属性的名称，因为活动输出具有多个属性的对象。

1. 单击 **"证书"，** 在参数值页上，进行以下设置，然后单击"**确定**"。

    * **数据源**-- 选择**活动输出**。
    * 数据源列表 - 选择**获取自动化连接**。
    * **字段路径**-`CertificateThumbprint`类型 。
1. 单击 **"服务"，** 在"参数值"页上，选择**数据源**字段的常量值;在"参数值"页上，选择"**恒定值**"字段。单击选项 **"True"** 然后单击 **"确定**"。
1. 单击**TENANTID**，并在"参数值"页上进行以下设置。 完成后，单击 **"确定"** 两次。

    * **数据源**-- 选择**活动输出**。 
    * 数据源列表 - 选择**获取自动化连接**。
    * **字段路径**-`TenantId`类型 。 
1. 在"库"控件中`Set-AzContext`，在搜索字段中键入。
1. 添加到`Set-AzContext`画布。
1. 在`Set-AzContext`画布上选择。 在"配置"控件窗格中`Specify Subscription Id`，在 **"标签"** 字段中输入。
1. 单击“参数”，此时会显示“活动参数配置”页****。
1. `Set-AzContext` cmdlet 具有多个参数集，您需要在提供参数值之前选择一个参数集。 单击**参数集**，然后选择 **"订阅 Id**"。
1. 此参数集的参数显示在"活动参数配置"页上。 单击**订阅 ID**。
1. 在"参数值"页上，为**数据源**字段选择**可变资产**，并从源列表中选择**Azure 订阅Id。** 完成后，单击 **"确定"** 两次。
1. 悬停，`Login to Azure`直到形状底部出现一个圆圈。 单击圆圈并将箭头拖动到`Specify Subscription Id`。 此时，Runbook 应如下所示。

    ![Runbook 身份验证配置](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>步骤 7 - 添加用于启动虚拟机的活动

现在，您必须添加活动`Start-AzVM`才能启动虚拟机。 您可以在 Azure 订阅中选择任何 VM，现在正在硬编码其名称到[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet 中。

1. 在"库"控件中`Start-Az`，在搜索字段中键入。
2. 添加到`Start-AzVM`画布，然后单击并拖动它在下方`Specify Subscription Id`。
1. 悬停，`Specify Subscription Id`直到形状底部出现一个圆圈。 单击圆圈并将箭头拖动到`Start-AzVM`。
1. 选择 `Start-AzVM`。 单击 **"参数"，** 然后单击**参数集**以查看活动的集。
1. 为参数集选择**资源组名称参数集名称**。 字段**资源组名称**和**名称**旁边有感叹号，以指示它们是必需的参数。 请注意，这两个字段都希望使用字符串值。
1. 选择**名称**。 为**数据源**字段选择**PowerShell 表达式**。 对于用于启动此 Runbook 的 VM，键入用双引号括起来的计算机名称。 单击“确定”。
1. 选择“ResourceGroupName”****。 使用**数据源**字段的值**PowerShell 表达式**，并键入用双引号括起来的资源组的名称。 单击“确定”。
1. 单击 **"测试"窗格**，以便可以测试 Runbook。
1. 单击 **"开始"** 以开始测试。 完成后，请确保 VM 已启动。 此时，Runbook 应如下所示。

    ![Runbook 身份验证配置](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>步骤 8 - 添加其他输入参数

Runbook 当前在为`Start-AzVM`cmdlet 指定的资源组中启动 VM。 如果在启动 Runbook 时同时指定名称和资源组，则 Runbook 将更有用。 让我们向 Runbook 添加输入参数以提供该功能。

1. 在“MyFirstRunbook-Graphical”窗格上单击“编辑”，打开图形编辑器********。
1. 依次选择“输入和输出”和“添加输入”，打开“Runbook 输入参数”窗格********。
1. 在提供的字段中进行以下设置，然后单击"**确定**"。
   * **名称**--`VMName`指定 。
   * **类型**-- 保留字符串设置。
   * **必修**- 将值更改为 **"是**"。
1. 创建第二个称为`ResourceGroupName`必需输入参数，然后单击 **"确定"** 以关闭"输入和输出"窗格。<br> ![Runbook 输入参数](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. 选择活动`Start-AzVM`，然后单击**参数**。
1. 将**名称**的**数据源**字段更改为**Runbook 输入**。 然后选择**VMName**。
1. 将**资源组名称**的**数据源**字段更改为**Runbook 输入**，然后选择 **"资源组名称**"。<br> ![启动-AzVM 参数](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. 保存 Runbook 并打开“测试”窗格。 现在可以为在测试中使用的两个输入变量提供值。
1. 关闭“测试”窗格。
1. 单击“发布”以发布 Runbook 的新版本 **** 。
1. 停止以前启动的 VM。
1. 单击“启动”以启动 Runbook **ResourceGroupName** 。 键入要启动的`VMName`VM`ResourceGroupName`的值和值。
1. 运行簿完成后，请确保 VM 已启动。

## <a name="step-9---create-a-conditional-link"></a>步骤 9 - 创建条件链接

您现在可以修改 Runbook，以便它仅在 VM 尚未启动时尝试启动它。 为此，添加检索 VM 实例级状态的[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) cmdlet。 然后，您可以添加使用 PowerShell 代码段`Get Status`调用的 PowerShell 工作流代码模块，以确定 VM 状态是正在运行还是停止。 仅当当前运行状态停止`Get Status`时，才会`Start-AzVM`运行模块的条件链接。 在此过程结束时，runbook 使用`Write-Output`cmdlet 输出消息来通知您 VM 是否已成功启动。

1. 在图形编辑器中打开“MyFirstRunbook-Graphical”****。
1. 单击 它，`Specify Subscription Id`然后`Start-AzVM`按 **"删除**"，删除 和 之间的链接。
1. 在"库"控件中`Get-Az`，在搜索字段中键入。
1. 添加到`Get-AzVM`画布。
1. 选择`Get-AzVM`然后单击 **"参数集"** 以查看 cmdlet 的集。 
1. **GetVirtualMachineInResourceGroupNameParamSet** 参数集。 **资源组名称**和**名称**字段旁边有感叹号，指示它们指定所需的参数。 请注意，这两个字段都希望使用字符串值。
1. 在 **"名称****"的数据源**下，选择**Runbook 输入**，然后**选择 VMName**。 单击“确定”。
1. 在 **"资源组名称****的数据源**"下，选择**Runbook 输入**，然后选择**资源组名称**。 单击“确定”。
1. 在 **"状态的数据源**"下，选择 **"常量"值**，然后**选择 True**。 **Status** 单击“确定”。
1. 从 创建从`Specify Subscription Id``Get-AzVM`到 的链接。
1. 在"库"控件中，展开**Runbook 控件**并将**代码**添加到画布。  
1. 从 创建从`Get-AzVM``Code`到 的链接。  
1. 单击`Code`"配置"窗格中，将标签更改为 **"获取状态**"。
1. 选择`Code`并显示代码编辑器页面。  
1. 将以下代码段粘贴到编辑器页中。

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. 从 创建从`Get Status``Start-AzVM`到 的链接。

    ![使用代码模块的 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 选择链接，并在"配置"窗格中将 **"将条件应用**到**是**"。 请注意，链接变为虚线，指示目标活动仅在条件解析为 true 时才运行。  
1. 对于**条件表达式**，键入`$ActivityOutput['Get Status'] -eq "Stopped"`。 `Start-AzVM`现在仅在停止 VM 时运行。
1. 在库控件中，展开 **Cmdlet** 节点，并展开 **Microsoft.PowerShell.Utility**。
1. 添加到`Write-Output`画布两次。
1. 对于第一`Write-Output`个控件，单击 **"参数**"并将**标签**值更改为 **"通知 VM 已启动**"。
1. 对于**InputObject，** 将**数据源**更改为**PowerShell 表达式**，并在表达式`$VMName successfully started.`中键入 。
1. 在第`Write-Output`二个控件上，单击 **"参数**"并将**标签**值更改为**通知 VM 启动失败**。
1. 对于**InputObject，** 将**数据源**更改为**PowerShell 表达式**，并在表达式`$VMName could not start.`中键入 。
1. 从`Start-AzVM`和`Notify VM Started`创建`Notify VM Start Failed`链接。
1. 选择指向`Notify VM Started`的链接并更改 **"将条件应用**为 true"。
1. 对于**条件表达式**，键入`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`。 仅当`Write-Output`VM 成功启动时，此控件才会运行。
1. 选择指向`Notify VM Start Failed`的链接并更改 **"将条件应用**为 true"。
1. 对于**条件表达式**字段，键入`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 仅当`Write-Output`VM 未成功启动时，此控件才运行。 Runbook 应类似于下图。

    ![使用 Write-Output 的 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. 保存 Runbook 并打开“测试”窗格。
1. 在 VM 停止时启动 Runbook，计算机应启动。

## <a name="next-steps"></a>后续步骤

* 要了解有关图形创作的更多信息，请参阅[Azure 自动化 中的图形创作](automation-graphical-authoring-intro.md)。
* 要开始使用 PowerShell 运行簿，请参阅[我的第一个 PowerShell 运行簿](automation-first-runbook-textual-powershell.md)。
* 要开始使用 PowerShell 工作流运行簿，请参阅[我的第一个 PowerShell 工作流运行簿](automation-first-runbook-textual.md)。