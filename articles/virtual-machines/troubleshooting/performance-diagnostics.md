---
title: Azure 虚拟机的性能诊断 | Microsoft Docs
description: 介绍适用于 Windows 的 Azure 性能诊断。
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: c2089f9f6267f318dafe641a6a5b22e7e87427ca
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441060"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure 虚拟机的性能诊断

性能诊断工具可帮助你排查可能会影响 Windows 虚拟机 (VM) 的性能问题。 支持故障排除方案包括快速检查已知问题和最佳做法，以及涉及到 VM 性能缓慢，或者 CPU、磁盘空间或内存使用率过高的复杂问题。 

可以直接从 Azure 门户运行性能诊断，在其中还可以查看见解，以及有关各种日志、丰富配置和诊断数据的报告。 我们建议在联系 Microsoft 支持部门之前，先运行性能诊断并查看见解和诊断数据。

> [!NOTE]
> 目前，装有 .NET SDK 4.5 或更高版本的 Windows VM 支持性能诊断。 有关在经典 VM 上运行性能诊断的步骤，请参阅[Azure 性能诊断 VM 扩展](performance-diagnostics-vm-extension.md)。

### <a name="supported-operating-systems"></a>支持的操作系统
Windows 10、Windows 8、Windows 8 Enterprise、Windows 8 Pro、Windows 8.1、Windows Server 2016、Windows Server 2012、Windows Server 2012 Datacenter、Windows Server 2012 R2、Windows Server 2012 R2 Datacenter、Windows Server 2012 R2 Standard、Windows Server 2012 Standard、Windows Server 2008 R2、Windows Server 2008 R2 Datacenter、Windows Server 2008 R2 Enterprise、Windows Server 2008 R2 Foundation、Windows Server 2008 R2 SP1、Windows Server 2008 R2 Standard。

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>在 VM 上安装并运行性能诊断
性能诊断会安装一个 VM 扩展，该扩展可运行名为 [PerfInsights](https://aka.ms/perfinsights) 的诊断工具。 若要安装并运行性能诊断，请执行以下步骤：
1.  在左侧的命令列中，选择“虚拟机”。
1.  在 VM 名称列表中，选择要对其运行诊断的 VM。
1.  在右侧的命令列中，选择“性能诊断”。

    ![Azure 门户的屏幕截图，其中突出显示了“安装性能诊断”按钮](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > 在此屏幕截图中，VM 名称的边栏选项卡是隐藏的。
1. 选择存储帐户（可选）

    若要使用单个存储帐户来存储多个 VM 的性能诊断结果，可在工具栏中单击“设置”按钮来选择存储帐户。 选择存储帐户后，单击“确定”按钮。

    如果未指定存储帐户，默认会创建一个新存储帐户。

    ![“性能诊断”边栏选项卡的屏幕截图，其中突出显示了“设置”工具栏按钮](media/performance-diagnostics/settings-button.png)

    ![在性能诊断设置边栏选项卡中选择存储帐户的屏幕截图](media/performance-diagnostics/select-storage-account.png)

1. 选择“安装性能诊断”按钮。
1. 若要在完成安装后运行诊断，请选中“运行诊断”复选框。 如果做出了此项选择，则可以选择性能分析方案和相关选项。

    ![性能诊断安装按钮的屏幕截图](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>选择要运行的分析方案

Azure 门户中提供了以下分析方案。 根据遇到的性能问题选择一个分析方案。 选择分析所需的持续时间和跟踪选项。

* **快速性能分析**  
    检查已知问题、分析最佳做法并收集诊断数据。 此分析需要几分钟的时间来运行。 [了解详细信息](https://aka.ms/perfinsights/quick)

* **性能分析**  
    包括快速性能分析中的所有检查，并监视资源消耗量较高的情况。 使用此版本可以排查一般性的性能问题，例如 CPU、内存和磁盘使用率较高的情况。 此项分析将花费 30 秒到 15 分钟，具体取决于选择的持续时间。 [了解详细信息](https://aka.ms/perfinsights/vmslow) 
    
* **高级性能分析**  
    包括性能分析中的所有检查，并收集下一部分中所列的一项或多项跟踪。 使用此方案可以排查需要更多跟踪的复杂问题。 长时间运行此方案会增大诊断输出的整体大小，具体输出大小取决于 VM 大小以及选择的跟踪选项。 运行此项分析将花费 30 秒到 15 分钟，具体取决于选择的持续时间。 [了解详细信息](https://aka.ms/perfinsights/advanced) 
    
* **Azure 文件分析**  
    包括性能分析中的所有检查，并捕获网络跟踪和 SMB 计数器。 使用此方案可以排查 Azure 文件的性能问题。 运行此项分析将花费 30 秒到 15 分钟，具体取决于选择的持续时间。 [了解详细信息](https://aka.ms/perfinsights/azurefiles)


![“性能诊断”边栏选项卡中的“运行诊断”窗格屏幕截图](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>提供症状（可选）
在列表中选择任何预先选择的症状，或添加新症状。 这有助于我们将来改进分析。 

### <a name="provide-support-request-number-if-available-optional"></a>提供支持请求编号，如果有（可选）
如果你正在就现有的支持票证与 Microsoft 支持工程师沟通，提供支持票证编号。 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>查看隐私政策和法律条款，然后选中相应的复选框以同意条款（必需）
若要运行诊断，必须同意法律条款并接受隐私政策。

### <a name="select-ok-to-run-the-diagnostics"></a>选择“确定”以运行诊断 
开始安装性能诊断时，会显示一条通知。 安装完成后，会显示一条通知，指出安装成功。 然后，选定的分析将运行指定的持续时间。 此时很适合再现性能问题，以便可以在适当的时间捕获诊断数据。 

分析完成后，以下各项将上传到 Azure 表以及存储帐户中指定的二进制大型对象 (BLOB) 容器：

*   有关运行的所有见解和信息
*   一个包含日志文件的输出压缩 (.zip) 文件（名为 **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**）
*   一份 HTML 报告

上传后，Azure 门户中会列出新的诊断报告。

![“性能诊断”边栏选项卡中诊断报告列表的屏幕截图](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>如何更改性能诊断设置
使用“设置”工具栏按钮来更改可以存储诊断见解和输出的存储帐户。 可对使用性能诊断的多个 VM 使用同一个存储帐户。 更改存储帐户时，不会删除旧的报告和见解。 但是，它们不再显示在诊断报告列表中。 

## <a name="review-insights-and-performance-diagnostics-report"></a>查看见解和性能诊断报告
每个诊断运行包含见解和建议的列表、受影响的资源、日志文件、其他收集的丰富诊断信息，以及供脱机查看的报告。 有关收集的所有诊断数据的完整列表，请参阅 [PerfInsights 收集哪类信息？](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>选择性能诊断报告
可以使用诊断报告列表来查找所有已运行的诊断报告。 该列表包含有关使用的分析、发现的见解及其影响级别的详细信息。 选择一行可查看更多详细信息。

![在“性能诊断”边栏选项卡中选择诊断报告的屏幕截图](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>查看性能诊断报告
每份性能诊断报告可能包含多项见解，并指示影响级别“高”、“中”或“低”。 每项见解还包含排忧解难的建议。 见解已分组以方便筛选。 

影响级别根据配置错误、已知问题或其他用户报告的问题来表示出现性能问题的可能性。 你可能尚未遇到所列出的一个或多个问题。 例如，你可能在同一个数据磁盘上存储了 SQL 日志文件和数据库文件。 在这种情况下，如果数据库使用率较高，则很有可能会出现瓶颈和其他性能问题，而使用率较低时，你可能察觉不到任何问题。

![性能诊断报告概述边栏选项卡的屏幕截图](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>查看性能诊断见解和建议
可以选择一个见解来查看有关受影响的资源、建议的缓解措施和参考链接的更多详细信息。 

![性能诊断见解详细信息的屏幕截图](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>下载并查看完整的性能诊断报告
可以使用“下载报告”按钮下载 HTML 报告，其中包含丰富的诊断信息，例如，存储和网络配置、性能计数器、跟踪、进程列表和日志。 其内容取决于所选的分析。 进行高级故障排除时，该报告可能包含有关 CPU 使用率较高、磁盘使用率较高和消耗过多内存的进程的其他信息和交互式图表。 有关性能诊断报告的详细信息，请参阅[查看诊断报告](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report)。

## <a name="manage-performance-diagnostics-reports"></a>管理性能诊断报告
可以使用“删除报告”按钮删除一个或多个性能诊断报告。

## <a name="how-to-uninstall-performance-diagnostics"></a>如何卸载性能诊断
可以在 VM 中卸载性能诊断。 此操作会删除 VM 扩展，但不会影响存储帐户中的任何诊断数据。 

![“性能诊断”边栏选项卡工具栏的屏幕截图，其中突出显示了“卸载”按钮](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>常见问题

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>VM 中的诊断数据在何处存储？ 
所有性能诊断见解和报告存储在你自己的存储帐户中。 见解存储在 Azure 表内部。 报告压缩文件存储在名为 azdiagextnresults 的二进制大型对象 (BLOB) 容器中。

可以使用工具栏上的“设置”按钮查看存储帐户信息。 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>如何与 Microsoft 客户支持部门共享此数据？ 
可通过多种方式来与 Microsoft 共享诊断报告。

**选项 1：** 自动共享最新的报表  
向 Microsoft 开具支持票证时，必须共享性能诊断报告。 如果你在运行诊断时已选择与 Microsoft 共享此信息（选中“我同意与 Microsoft 共享诊断信息”复选框），则 Microsoft 可以在从运行日期开始算起的最长 30 天内，使用输出 zip 文件的 SAS 链接从你的存储帐户访问报告。 只会将最新的报告提供给支持工程师。 

**选项 2：** 生成诊断报告压缩文件的共享访问签名  
可以使用共享访问签名来共享报告压缩文件的链接。 为此，请执行以下步骤： 
1.  在 Azure 门户中，浏览到存储了诊断数据的存储帐户。
1.  在“Blob 服务”部分选择“Blob”。 
1.  选择“azdiagextnresults”容器。
1.  选择要共享的性能诊断输出压缩文件。
1.  在“生成 SAS”选项卡上，选择共享条件。 
1.  单击“生成 Blob SAS 令牌和 URL”。
1.  复制“Blob SAS URL”并与支持工程师共享。 

**选项 3:** 从存储帐户下载报表

也可以使用“选项 2”中的步骤 1-4 找到性能诊断报告压缩文件。 选择下载文件，然后通过电子邮件共享该文件，或者请求支持工程师提供有关上传文件的说明。  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>如何在适当的时间捕获诊断数据？
每个性能诊断运行包括两个阶段： 
1.  安装或更新性能诊断 VM 扩展。
1.  将诊断运行指定的持续时间。

目前难以确切地知道 VM 扩展安装何时完成。 安装 VM 扩展通常需要大约 45 秒到 1 分钟时间。 安装 VM 扩展后，可以运行再现步骤，让性能诊断捕获正确的数据集进行故障排除。 

## <a name="next-steps"></a>后续步骤
查看性能诊断见解和报告后，如果仍不能确定问题的原因并需要更多的帮助，可以向 Microsoft 客户支持部门开具支持票证。 

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
