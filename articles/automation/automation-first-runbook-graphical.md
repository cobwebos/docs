---
title: 我在 Azure 自动化中的第一个图形 Runbook
description: 本教程指导完成创建、 测试和发布一个简单图形 Runbook。
keywords: runbook, runbook 模板, runbook 自动化, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 351ed146511409fb4c6cc49705c7051b89ea0212
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226485"
---
# <a name="my-first-graphical-runbook"></a>我的第一个图形 runbook

> [!div class="op_single_selector"]
> * [图形](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

本教程将指导你在 Azure 自动化中创建[图形 runbook](automation-runbook-types.md#graphical-runbooks) 。 从一个简单的 runbook 开始，其中测试和发布，同时了解如何跟踪 runbook 作业的状态。 然后，将 runbook 修改为实际管理 Azure 资源，在这种情况下，将启动 Azure 虚拟机。 完成本教程，通过添加 runbook 参数和条件链接使 runbook 更可靠。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 由于你停止并启动此计算机，因此不应为生产 VM。

## <a name="step-1---create-runbook"></a>步骤 1 - 创建 Runbook

首先创建一个输出文本 "Hello World" 的简单 runbook。

1. 在 Azure 门户中，打开自动化帐户。 

    通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 其中的大多数资产都是自动包括在新的自动化帐户中的模块。 还应提供与订阅关联的凭据资产。
2. 选择 "**流程自动化**" 下的 " **runbook** "，打开 runbook 的列表。
3. 通过选择 "**创建 runbook**" 来创建新的 runbook。
4. 将该 Runbook 命名为 **MyFirstRunbook-Graphical**。
5. 在这种情况下，将创建[图形 runbook](automation-graphical-authoring-intro.md)。 选择 "**图形**" 作为 " **Runbook 类型**"。<br> ![新建 Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. 单击“创建”以创建 Runbook 并打开图形编辑器 。

## <a name="step-2---add-activities"></a>步骤 2-添加活动

编辑器左侧的库控件允许选择要添加到 Runbook 的活动。 需要添加 **Write-Output** cmdlet 以从 Runbook 输出文本。

1. 在 "库" 控件中，单击搜索字段，然后键入 "**写入输出**"。 搜索结果如下图所示。 <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 向下滚动到列表的底部。 右键单击 "**写入-输出**"，然后选择 "**添加到画布**"。 或者，可以单击 cmdlet 名称旁边的省略号（...），然后选择 "**添加到画布**"。
1. 单击画布上的 **Write-Output** 活动。 此操作会打开可用于配置活动的“配置控件”页。
1. "**标签**" 字段默认为该 cmdlet 的名称，但你可以将其更改为更友好的名称。 将其更改为 **Write Hello World to output**。
1. 单击“参数”为 cmdlet 参数提供值。

   某些 cmdlet 有多个参数集，并且需要选择要使用的参数集。 在这种情况下，**写入输出**只设置一个参数。

1. 选择 *InputObject* 参数。 这是用于指定要发送到输出流的文本的参数。
1. "**数据源**" 下拉菜单提供可用于填充参数值的源。 在此菜单中，选择 " **PowerShell 表达式**"。 

   可以使用来自其他活动、自动化资产或 PowerShell 表达式的源的输出。 在这种情况下，输出就是 **Hello World**。 可以使用 PowerShell 表达式并指定一个字符串。<br>

1. 在 "**表达式**" 字段中，键入**Hello World**然后单击 **"确定"** 两次以返回到画布。
1. 通过单击“保存”保存 Runbook。

## <a name="step-3---test-the-runbook"></a>步骤 3 - 测试 Runbook

在发布 runbook 以使其在生产中可用之前，应该对其进行测试以确保其正常运行。 测试 runbook 将运行其草稿版本，并允许你以交互方式查看其输出。

1. 选择 "**测试窗格**" 打开 "测试" 窗格。
1. 单击“启动”以启动测试 。 这应该是唯一的已启用选项。
1. 请注意，将创建一个[runbook 作业](automation-runbook-execution.md)并在窗格中显示其状态。

   作业状态将作为**排队**开始，指示作业正在等待云中的 runbook 辅助角色变为可用。 当工作线程声明作业时，状态将更改为 "**正在启动**"。 最后，当 runbook 实际开始运行时，状态将变为 "**正在运行**"。

1. Runbook 作业完成后，"测试" 页将显示其输出。 在这种情况下，会看到 **Hello World**。<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 关闭测试窗格以返回到画布。

## <a name="step-4---publish-and-start-the-runbook"></a>步骤 4 - 发布和启动 Runbook

你创建的 runbook 仍处于草稿模式。 必须先将它发布，然后才能在生产环境中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 选择“发布”以发布该 Runbook，并在出现提示时选择“是”。
1. 向左滚动以在 "Runbook" 页上查看 runbook，并注意 "**创作状态**" 值已设置为 "**已发布**"。
1. 往回向右滚动查看“MyFirstRunbook-Graphical”页。

   通过顶部的选项，你可以立即启动 runbook，计划将来的开始时间，或创建[webhook](automation-webhooks.md)以便可以通过 HTTP 调用启动 runbook。

1. 选择“启动”，并在出现提示时选择“是”启动 runbook。
1. 将为已创建的 runbook 作业打开作业窗格。 验证 "**作业状态**" 字段是否显示 "**已完成**"。
1. 单击 "**输出**" 打开 "输出" 页，您可以在其中看到**Hello World**显示。
1. 关闭“输出”页。
1. 单击“所有日志”打开 Runbook 作业的“流”窗格。 只应在输出流中看到**Hello World** 。 

    请注意，"流" 窗格可以显示 runbook 作业的其他流，如 "详细" 和 "错误流" （如果 runbook 向其写入）。
1. 关闭 "流" 窗格和 "作业" 窗格以返回到**MyFirstRunbook-图形**页面。
1. 若要查看 runbook 的所有作业，请在 "**资源**" 下选择 "**作业**"。 "作业" 页列出了 runbook 创建的所有作业。 你应该只会看到一个列出的作业，因为你只运行一次作业。
1. 单击作业名称，打开在启动 runbook 时查看的相同的作业窗格。 使用此窗格查看为 runbook 创建的任何作业的详细信息。

## <a name="step-5---create-variable-assets"></a>步骤 5 - 创建变量资产

已测试并发布 runbook，但到目前为止，管理 Azure 资源不会有任何用处。 在将 runbook 配置为进行身份验证之前，你必须创建一个变量来保存订阅 ID、设置一个用于身份验证的活动，然后引用该变量。 通过包括对订阅上下文的引用，可以轻松地使用多个订阅。

1. 从导航窗格上的 "**订阅**" 选项中复制订阅 ID。
1. 在 "自动化帐户" 页中，选择 "**共享资源**" 下的 "**变量**"。
1. 选择“添加变量”。
1. 在 "新建变量" 页上，在提供的字段中进行以下设置。

    * **名称**--输入**AzureSubscriptionId**。
    * **值**-输入订阅 ID。 
    * **类型**--保留字符串处于选中状态。
    * **加密**--使用默认值。
1. 单击“创建”以创建该变量。

## <a name="step-6---add-authentication"></a>步骤 6-添加身份验证

现在，你已有一个用于保存订阅 ID 的变量，可以将 runbook 配置为使用你的订阅的 "运行方式凭据" 进行身份验证。 通过将 Azure 运行方式连接作为资产添加来实现此目的。 还必须将[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) Cmdlet 和[AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) cmdlet 添加到画布中。

>[!NOTE]
>对于 PowerShell runbook， **AzAccount**和**add-azurermaccount**是**AzAccount**的别名。 请注意，这些别名不适用于图形 runbook。 图形 runbook 只能使用**AzAccount**本身。

1. 导航到 runbook，然后在 " **MyFirstRunbook** " 页上选择 "**编辑**"。
1. 你不需要**写入 Hello World 输出**条目。 只需单击省略号，然后选择 "**删除**"。
1. 在库控件中，依次展开 "**资产**"、"**连接**"。 通过选择 "**添加到画布**" 将**AzureRunAsConnection**添加到画布。
1. 在 "库" 控件中，在搜索字段中键入**AzAccount** 。
1. 将**AzAccount**添加到画布。
1. 将鼠标悬停在“获取运行方式连接”上方，直到在该形状的底部显示一个圆圈。 单击圆圈，并拖动箭头以**连接-AzAccount**以形成链接。 Runbook 从 "**获取运行方式连接**" 开始，然后运行**AzAccount**。<br> ![创建活动之间的链接](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 在画布上，选择 " **AzAccount**"。 在 "配置" 控件窗格中，在 "**标签**" 字段中键入 "**登录到 Azure** "。
1. 单击 "**参数**"，此时将显示 "活动参数配置" 页。
1. **AzAccount** cmdlet 具有多个参数集，并且需要先选择一个参数集，然后才能提供参数值。 单击“参数集”，并选择“ServicePrincipalCertificate”参数集。
1. 此参数集的参数会显示在 "活动参数配置" 页上。 单击“APPLICATIONID”。<br> ![添加 Azure 帐户参数](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 在 "参数值" 页上，进行以下设置，然后单击 **"确定"** 。

   * **数据源**--选择 "**活动输出**"。
   * 数据源列表-选择 "**获取运行方式连接**"。
   * **字段路径**--类型**ApplicationId**。 您正在指定字段路径的属性的名称，因为该活动输出的对象具有多个属性。
1. 单击 " **CERTIFICATETHUMBPRINT**"，然后在 "参数值" 页上，进行以下设置，然后单击 **"确定"** 。

    * **数据源**--选择 "**活动输出**"。
    * 数据源列表-选择 "**获取运行方式连接**"。
    * **字段路径**--类型**CertificateThumbprint**。
1. 单击 " **SERVICEPRINCIPAL**"，然后在 "参数值" 页上，为 "**数据源**" 字段选择 " **constantvalue 作为**";单击选项**True**;然后单击 **"确定"** 。
1. 单击 " **TENANTID**"，并在 "参数值" 页上进行以下设置。 完成后，单击 **"确定"** 两次。

    * **数据源**--选择 "**活动输出**"。 
    * 数据源列表-选择 "**获取运行方式连接**"。
    * **字段路径**-类型**TenantId**。 
1. 在 "库" 控件中，在搜索字段中键入**AzContext** 。
1. 将**AzContext**添加到画布。
1. 在画布上选择 " **AzContext** "。 在 "配置" 控件窗格中，在 "**标签**" 字段中输入 "**指定订阅 Id** "。
1. 单击“参数”，此时会显示“活动参数配置”页。
1. **AzContext** cmdlet 具有多个参数集，并且需要先选择一个参数集，然后才能提供参数值。 单击“参数集”，并选择“SubscriptionId”参数集。
1. 此参数集的参数会显示在 "活动参数配置" 页上。 单击 " **SubscriptionID**"。
1. 在 "参数值" 页上，为 "**数据源**" 字段选择 "**变量资产**"，然后从 "源" 列表中选择 " **AzureSubscriptionId** "。 完成后，单击 **"确定"** 两次。
1. 将鼠标悬停在“登录到 Azure”上方，直到在该形状的底部显示一个圆圈。 **Specify Subscription Id**。 

此时，Runbook 看起来将如下所示： <br>![Runbook 身份验证配置](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>步骤 7 - 添加用于启动虚拟机的活动

现在，你必须添加**new-azvm**活动以启动虚拟机。 你可以在 Azure 订阅中选取任何 VM，现在你可以将其名称硬编码为[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet。

1. 在 "库" 控件中，在搜索字段中键入 " **Start-Az** "。
2. 将**new-azvm**添加到画布，然后在 "**指定订阅 Id**" 下单击并拖动它。
1. 将鼠标悬停在 **Specify Subscription Id** 上方，直到在该形状的底部显示一个圆圈。 单击该圆圈，然后将箭头拖到 " **new-azvm**"。
1. 选择**new-azvm**。 单击 "**参数**"，然后选择 "**参数设置**" 以查看活动集。
1. **ResourceGroupNameParameterSetName** 参数集。 字段**ResourceGroupName**和**Name**旁边带有惊叹号，指示它们是必需的参数。 请注意，这两个字段都需要字符串值。
1. 选择“名称”。 为 "**数据源**" 字段选择 " **PowerShell 表达式**"。 对于用于启动此 runbook 的 VM，键入计算机名称，用双引号引起来。 单击“确定”。
1. 选择“ResourceGroupName”。 为 "**数据源**" 字段使用 " **PowerShell 表达式**"，然后键入用双引号引起来的资源组的名称。 单击“确定”。
1. 单击“测试”窗格，以便测试 Runbook。
1. 单击 "**启动**" 以开始测试。 完成后，请确保已启动 VM。 

此时，Runbook 看起来将如下所示： <br>![Runbook 身份验证配置](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>步骤 8-添加其他输入参数

你的 runbook 当前在你为**new-azvm** cmdlet 指定的资源组中启动 VM。 如果在启动 runbook 时指定了名称和资源组，runbook 将更有用。 让我们向 runbook 添加输入参数以提供该功能。

1. 在“MyFirstRunbook-Graphical”窗格上单击“编辑”，打开图形编辑器。
1. 依次选择“输入和输出”和“添加输入”，打开“Runbook 输入参数”窗格。
1. 在提供的字段中进行以下设置，然后单击 **"确定"** 。
   * **Name** --指定**VMName**。
   * **类型**--保留字符串设置。
   * **必需**--将值更改为**Yes**。
1. 创建名为*ResourceGroupName*的第二个强制输入参数，然后单击 **"确定"** 以关闭 "输入和输出" 窗格。<br> ![Runbook 输入参数](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. 选择**new-azvm**活动，并单击 "**参数**"。
1. 将 "**名称**" 的 "**数据源**" 字段更改为 " **Runbook 输入**"。 然后选择 " **VMName**"。
1. 将**ResourceGroupName**的 "**数据源**" 字段更改为 " **Runbook 输入**"，然后选择 " **ResourceGroupName**"。<br> ![New-azvm 参数](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. 保存 Runbook 并打开“测试”窗格。 现在可以为在测试中使用的两个输入变量提供值。
1. 关闭“测试”窗格。
1. 单击“发布”以发布 Runbook 的新版本 。
1. 停止之前启动的 VM。
1. 单击“启动”以启动 Runbook **ResourceGroupName** 。 键入要启动的虚拟机的**VMName**和**ResourceGroupName**的值。
1. Runbook 完成后，请确保已启动 VM。

## <a name="step-9---create-a-conditional-link"></a>步骤 9 - 创建条件链接

你现在可以修改 runbook，以便它仅尝试启动 VM （如果尚未启动）。 为此，请添加检索 VM 的实例级状态的[new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) cmdlet。 然后，可以添加一个名为 "**获取状态**" 的 Powershell 工作流代码模块，其中包含 powershell 代码片段，以确定 VM 状态是正在运行还是已停止。 如果当前正在运行的状态为 "已停止"，则从**Get Status**模块开始的条件链接将仅运行**new-azvm** 。 完成此过程后，runbook 将使用**写入-输出**cmdlet 来输出消息，以便在 VM 已成功启动时发出通知。

1. 在图形编辑器中打开“MyFirstRunbook-Graphical”。
1. 单击 "**指定订阅 Id** " 和 " **new-azvm** " 之间的链接，方法是单击该 Id 并按**Delete**。
1. 在 "库" 控件中，在搜索字段中键入**get-help** 。
1. 将**new-azvm**添加到画布。
1. 选择 " **new-azvm** "，然后选择 "**参数设置**" 以查看 cmdlet 的集。 
1. **GetVirtualMachineInResourceGroupNameParamSet** 参数集。 " **ResourceGroupName** " 和 " **Name** " 字段旁边有感叹号，指示它们指定了所需的参数。 请注意，这两个字段都需要字符串值。
1. 在 "**名称**" 的 "**数据源**" 下，选择 " **Runbook 输入**"，然后选择**VMName**。 单击“确定”。
1. 在**ResourceGroupName**的 "**数据源**" 下，选择 " **Runbook 输入**"，然后选择 " **ResourceGroupName**"。 单击“确定”。
1. 在 "**数据源**"**状态**下，选择 "**常数值**"，然后选择 " **True**"。 单击“确定”。
1. 创建从**指定订阅 Id**到**new-azvm**的链接。
1. 在 "库" 控件中，展开 " **Runbook 控制**" 并将**代码**添加到画布。  
1. 创建从**new-azvm**到**代码**的链接。  
1. 单击 "**代码**"，然后在 "配置" 窗格中将标签更改为 "**获取状态**"。
1. 此时会显示 "选择**代码**" 和 "代码编辑器" 页。  
1. 将以下代码片段粘贴到编辑器页中。

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

1. 创建从**Get Status**到**new-azvm**的链接。<br> ![使用代码模块的 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 选择该链接，然后在 "配置" 窗格中将 "**应用条件**" 更改为 **"是"** 。 请注意，该链接将变为虚线，指示仅在条件解析为 true 时才会运行目标活动。  
1. 对于 "**条件表达式**"，请键入 `$ActivityOutput['Get Status'] -eq "Stopped"`。 **New-azvm**仅在 VM 停止时才会运行。
1. 在库控件中，展开 **Cmdlet** 节点，并展开 **Microsoft.PowerShell.Utility**。
1. 将 **Write-Output** 添加到画布两次。
1. 对于第一个**写入输出**控件，请单击 "**参数**" 并将 "**标签**" 值更改为 "**通知 VM 已启动**"。
1. 对于**InputObject**，将 "**数据源**" 更改为 " **PowerShell 表达式**"，然后在表达式中键入 **$VMName 成功启动。**
1. 在第二个**写入输出**控件上，单击 "**参数**" 并将 "**标签**" 值更改为 "**通知 VM 启动失败**"。
1. 对于**InputObject**，将 "**数据源**" 更改为 " **PowerShell 表达式**"，并键入表达式 " **$VMName 无法启动"。**
1. 创建**new-azvm**的链接，**通知 vm 已启动**并**通知 vm 启动失败**。
1. 选择 "**通知 VM 已启动**" 的链接，并将 "**应用条件**" 更改为 true。
1. 对于 "**条件表达式**"，请键入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`。 此**写入输出**控件现在仅在 VM 成功启动的情况下运行。
1. 选择 "**通知 VM 启动失败**" 链接，并将 "**应用条件**" 更改为 true。
1. 对于 "**条件表达式**" 字段，请键入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 此**写入输出**控件现在仅在 VM 未成功启动的情况下运行。 Runbook 应看起来如下图所示： <br> ![使用 Write-Output 的 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. 保存 Runbook 并打开“测试”窗格。
1. 在 VM 停止的情况下启动 runbook，计算机应启动。

## <a name="next-steps"></a>后续步骤

* 若要了解有关图形创作的详细信息，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
* 若要开始使用 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要开始 PowerShell 工作流 runbook，请参阅[我的第一个 PowerShell 工作流 runbook](automation-first-runbook-textual.md)。