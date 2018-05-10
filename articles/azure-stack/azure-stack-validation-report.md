---
title: Azure 堆栈的验证报告 |Microsoft 文档
description: 使用 Azure 堆栈准备情况检查程序报表来查看验证结果。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-validation-report"></a>Azure 堆栈验证报告
使用 Azure 堆栈准备情况检查器工具运行支持的部署和维护 Azure 堆栈环境的验证。 该工具将写入.json 报表文件的验证结果。 报表显示整个详细和汇总数据和机密旋转上部署的 Azure 堆栈的先决条件的状态，对于现有的 Azure 堆栈部署。  

 ## <a name="where-to-find-the-report"></a>在哪里可以找到报表
工具运行时，其结果记录到**AzsReadinessCheckerReport.json**。 该工具还创建名为日志**AzsReadinessChecker.log**。 使用 PowerShell 中的验证结果显示这些文件的位置。

![运行验证](./media/azure-stack-validation-report/validation.png)

这两个文件保留在同一台计算机上运行时的后续验证检查的结果。  例如，可以运行该工具以验证证书，再次运行以验证 Azure 标识，然后第三次来验证注册。 在生成的.json 报表中获得的所有三个验证这些结果。  

默认情况下，这两个文件写入到*C:\Users\<用户名 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
- 使用 **-OutputPath** ***&lt;路径&gt;*** 运行的命令行，以指定不同的报表位置结尾的参数。   
- 使用 **-CleanReport**参数运行的命令以清除信息从末尾*AzsReadinessCheckerReport.json*。 有关以前运行的工具。

## <a name="view-the-report"></a>查看报告
若要在 PowerShell 中查看报表，提供报表的路径的值作为 **-ReportPath**。 此命令显示报表的内容，并还会标识还没有结果的验证。

例如，若要查看报表从报表所在的位置打开的 PowerShell 提示符，运行： 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

输出类似于下图：

![查看报表](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>查看摘要报表
若要查看报表的摘要，你可以添加 **-摘要**切换到 PowerShell 命令行的末尾。 例如： 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

摘要显示没有结果的验证，并指示通过或失败的都已完成的验证。 输出类似于下图：

![报表摘要](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>查看已筛选的表
若要查看在单一类型的验证上筛选报表，请使用 **-ReportSections**参数并指定对应于你想要查看的验证类型的以下值之一：
- 证书
- AzureRegistration
- AzureIdentity
- 作业   
- 全部  

例如，若要查看报表的证书摘要只能，使用以下 PowerShell 命令行： 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>另请参阅
[开始 AzsReadinessChecker cmdlet 参考](azure-stack-azsreadiness-cmdlet.md)
