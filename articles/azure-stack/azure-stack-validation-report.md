---
title: 针对 Azure Stack 的验证报表 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器报表查看验证结果。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 156b84e4941363716721b5cee6c19333ffe7594c
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079440"
---
# <a name="azure-stack-validation-report"></a>Azure Stack 验证报表
使用 Azure Stack 就绪性检查器工具运行支持的部署和维护的 Azure Stack 环境的验证。 该工具将结果写入到.json 报表文件。 此报表显示有关状态的 Azure Stack 部署先决条件的详细和汇总数据。 报表还显示有关机密轮换为现有的 Azure Stack 部署的信息。  

 ## <a name="where-to-find-the-report"></a>在何处可以找到该报表
该工具运行时，它会将结果记录到 **AzsReadinessCheckerReport.json** 中。 该工具还会创建一个名为 **AzsReadinessChecker.log** 的日志。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

![运行验证](./media/azure-stack-validation-report/validation.png)

当在同一计算机上运行后续验证检查时，这两个文件都会持久保留这些验证检查的结果。  例如，可以运行该工具来验证证书，再次运行它来验证 Azure 标识，第三次运行它来验证注册。 所有三次验证的结果都在生成的 .json 报表中。  

默认情况下，这两个文件都写入到 *C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
- 可以在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数指定一个不同的报表位置。   
- 可以在运行命令的末尾使用 **-CleanReport** 参数从 *AzsReadinessCheckerReport.json* 中清除 有关该工具的以前运行的信息。

## <a name="view-the-report"></a>查看报告
若要在 PowerShell 中查看报表，请将报表路径提供为 **-ReportPath** 的值。 此命令显示报表的内容，并标识还没有结果的验证。

例如，若要从打开到报表所在位置的 PowerShell 提示符查看报表，请运行以下命令： 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

输出如下图所示：

![查看报表](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>查看报表摘要
若要查看报表摘要，可以在 PowerShell 命令行的末尾添加 **-Summary** 开关。 例如： 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

摘要会显示没有结果的验证并且会指明已完成的验证是通过还是失败。 输出如下图所示：

![报表摘要](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>查看经筛选的报表
若要查看已筛选的报表上是单一类型的验证，请使用 **-ReportSections**参数使用以下值之一：
- 证书
- AzureRegistration
- AzureIdentity
- 作业   
- 全部  

例如，若要仅查看证书的报表摘要，请使用以下 PowerShell 命令行： 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>另请参阅
[Start-AzsReadinessChecker cmdlet 参考](azure-stack-azsreadiness-cmdlet.md)
