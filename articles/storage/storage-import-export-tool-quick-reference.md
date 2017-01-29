---
title: "Azure 导入/导出工具的导入作业命令的快速参考 | Microsoft Docs"
description: "导入作业常用的 Azure 导入/导出工具命令参考"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>导入作业的常用命令快速参考

本文提供一些常用命令的快速参考。 有关详细用法，请参阅[为导入作业准备硬盘驱动器](storage-import-export-tool-preparing-hard-drives-import.md)。

## <a name="import-job-quick-reference"></a>导入作业快速参考

对于第一个会话：

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

第二个会话：

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

中止最新会话：

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

恢复最近中断的会话：

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

将驱动器添加到最新的会话：

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>后续步骤

[为导入作业准备硬盘驱动器的示例工作流](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)



<!--HONumber=Dec16_HO3-->


