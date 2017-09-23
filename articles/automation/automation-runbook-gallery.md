---
title: "Azure 自动化的 Runbook 和模块库 | Microsoft Docs"
description: "你可以安装并在 Azure 自动化环境中使用 Microsoft 和社区提供的 Runbook 与模块。  本文介绍如何访问这些资源，以及在库中补充你的 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: magoedte;bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: e6033f089152c27713f98f12634c53012f5ff070
ms.openlocfilehash: 04cfafc9e7a037d915f63723fd0b67a07954460b
ms.contentlocale: zh-cn
ms.lasthandoff: 12/16/2016

---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure 自动化的 Runbook 和模块库
你无需在 Azure 自动化中创建自己的 Runbook 和模块，即可访问 Microsoft 和社区构建的各种方案。  你可以在不加以修改的情况下直接使用这些方案，或者使用它们作为起点并根据具体的要求进行编辑。

可以从 [Runbook 库](#runbooks-in-runbook-gallery)获取 Runbook，并从[PowerShell 库](#modules-in-powerShell-gallery)获取模块。  你还可以通过共享开发的方案来为社区做出贡献。

## <a name="runbooks-in-runbook-gallery"></a>Runbook 库中的 Runbook
[Runbook 库](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation)提供各种来自 Microsoft 的 Runbook，以及可导入 Azure 自动化中的社区。 可以从 [TechNet 脚本中心](https://gallery.technet.microsoft.com/scriptcenter/site/upload)托管的库下载 Runbook，或者在 Azure 经典门户或 Azure 门户中直接从该库导入 Runbook。

直接从 Runbook 库导入只能使用 Azure 经典门户或 Azure 门户来完成。 而不能使用 Windows PowerShell 执行此功能。

> [!NOTE]
> 你应验证从 Runbook 库获取的任何 Runbook 的内容，在生产环境中安装和运行这些 Runbook 时，请谨慎操作。
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-classic-portal"></a>使用 Azure 经典门户从 Runbook 库导入 Runbook
1. 在 Azure 门户中，依次单击“新建”、“应用程序服务”、“自动化”、“Runbook”、“从库中”。
2. 选择一个类别以查看相关的 Runbook，然后选择一个 Runbook 以查看其详细信息。 选择所需的 Runbook 后，单击右箭头按钮。
   
    ![Runbook 库](media/automation-runbook-gallery/runbook-gallery.png)
3. 查看该 Runbook 的内容，并记下说明中所述的任何要求。 完成后，单击右箭头按钮。
4. 输入 Runbook 详细信息，然后单击复选标记按钮。 Runbook 名称已填入。
5. 该 Runbook 将出现在自动化帐户的“Runbook”选项卡中。

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 门户从 Runbook 库导入 Runbook
1. 在 Azure 门户中，打开你的自动化帐户。
2. 单击“Runbook”  磁贴打开 Runbook 的列表。
3. 单击“浏览库”按钮。
   
    ![“浏览库”按钮](media/automation-runbook-gallery/browse-gallery-button.png)
4. 找到所需的库项，选择它以查看其详细信息。
   
    ![浏览库](media/automation-runbook-gallery/browse-gallery.png)
5. 单击“查看源项目”以在 [TechNet 脚本中心](http://gallery.technet.microsoft.com/)查看该项。
6. 若要导入项，请单击它以查看其详细信息，然后单击“导入”按钮。
   
    ![“导入”按钮](media/automation-runbook-gallery/gallery-item-detail.png)
7. 可选择更改 Runbook 的名称，然后单击“确定”导入该 Runbook。
8. 该 Runbook 将出现在自动化帐户的“Runbook”选项卡中。

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>将 Runbook 添加到 Runbook 库
Microsoft 建议你将 Runbook 添加到你认为对其他客户有用的 Runbook 库中。  可通过[将 Runbook 上传到脚本中心](http://gallery.technet.microsoft.com/site/upload)来添加它，并同时考虑以下详细信息。

* 必须为向导中要显示的 Runbook 指定“Microsoft Azure”作为“类别”，指定“自动化”作为“子类别”。  
* 上载内容必须是单个 .ps1 或 .graphrunbook 文件。  如果 Runbook 需要任何模块、子 Runbook 或资产，则你应该在提交内容的说明和 Runbook 的注释部分列出这些内容。  如果你的方案需要多个 Runbook，请单独上载每个 Runbook 并在各自的说明中列出相关 Runbook 的名称。 请确保使用相同的标记，以便它们在同一类别中显示。 用户阅读说明后才会知道，方案要正常工作需要其他 Runbook。
* 如果要发布“图形 Runbook”（非图形工作流），则添加标记“GraphicalPS”。 
* 使用“插入代码段”图标将 PowerShell 或 PowerShell 工作流代码片段插入说明中。
* Runbook 库结果中将显示上载摘要，因此，你应该提供详细信息，以帮助用户了解 Runbook 的功能。
* 你应该为上载内容分配一到三个以下标记。  Runbook 将在向导中与标记匹配的类别下列出。  该向导将忽略不在此列表中的所有标记。 如果你未指定任何匹配的标记，则 Runbook 将在“其他”类别下列出。
  
  * 备份
  * 容量管理
  * 更改控制
  * 合规性
  * 开发/测试环境
  * 灾难恢复
  * 监视
  * 修补
  * 设置
  * 补救
  * VM 生命周期管理
* 自动化每小时更新一次该库，因此，你无法立即看见上载内容。

## <a name="modules-in-powershell-gallery"></a>PowerShell 库中的模块
PowerShell 模块包含可以在 Runbook 中使用的 cmdlet，并可以在 [PowerShell 库](http://www.powershellgallery.com)中找到可在 Azure 自动化中安装的现有模块。  你可以从 Azure 门户启动此库，并将这些模块直接安装到 Azure 自动化中，也可以下载并手动安装这些模块。  不能直接从 Azure 经典门户安装这些模块，但可以在下载之后进行安装，就像使用其他模块一样。

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>使用 Azure 门户从自动化模块库导入模块
1. 在 Azure 门户中，打开你的自动化帐户。
2. 单击“资产”磁贴以打开资产列表。
3. 单击“模块”磁贴以打开模块列表。
4. 单击“浏览库”按钮，此时将启动“浏览库”边栏选项卡。
   
    ![模块库](media/automation-runbook-gallery/modules-blade.png) <br>
5. 启动“浏览库”边栏选项卡后，可以按以下字段进行搜索：
   
   * 模块名称
   * 标记
   * 作者
   * Cmdlet/DSC 资源名称
6. 找到你感兴趣的模块并选择它以查看其详细信息。  
   当钻取到特定模块时，你可以查看有关该模块的详细信息，其中包含返回 PowerShell 库的链接、任何必需的依赖项，以及该模块包含的所有 cmdlet 和/或 DSC 资源。
   
    ![PowerShell 模块详细信息](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. 若要直接将模块安装到 Azure 自动化中，请单击“导入”按钮。
   
    ![“导入模块”按钮](media/automation-runbook-gallery/module-import-button.png)
8. 单击“导入”按钮时，你将看到将要导入的模块名称。 如果安装了所有依赖项，“确定”按钮将处于活动状态。 如果缺少依赖项，则需要在导入此模块前导入这些依赖项。
9. 单击“确定”以导入该模块，此时将启动“模块”边栏选项卡。 Azure 自动化将模块导入到你的帐户时，它将提取有关该模块和 cmdlet 的元数据。
   
    ![“导入模块”边栏选项卡](media/automation-runbook-gallery/module-import-blade.png)
   
    这可能需要几分钟才能完成，因为需要提取每个活动。
10. 完成后，你将收到正在部署该模块的通知。
11. 导入模块后，你将看到可用的活动，并可以在 Runbook 和所需状态配置中使用其资源。

## <a name="requesting-a-runbook-or-module"></a>请求 Runbook 或模块
可以将请求发送到[用户之声](https://feedback.azure.com/forums/246290-azure-automation/)。  如果需要 Runbook 编写帮助，或对 PowerShell 存有疑问，请将问题发布到我们的[论坛](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)。

## <a name="next-steps"></a>后续步骤
* 若要开始使用 Runbook，请参阅[在 Azure 自动化中创建或导入 Runbook](automation-creating-importing-runbook.md)
* 若要了解 Runbook 的 PowerShell 和 PowerShell 工作流之间的差异，请参阅[了解 PowerShell 工作流](automation-powershell-workflow.md)


