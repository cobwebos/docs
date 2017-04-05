---
title: "Azure 导入/导出工具的导入作业命令快速参考 | Microsoft Docs"
description: "Azure 导入/导出工具的常用导入作业命令参考。"
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
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e9377e0c5001cf5be220e19e06ff96c1e058e853
ms.lasthandoff: 03/30/2017


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>导入作业的常用命令快速参考

本文提供一些常用命令的快速参考。 有关详细用法，请参阅[为导入作业准备硬盘驱动器](storage-import-export-tool-preparing-hard-drives-import.md)。

## <a name="first-session"></a>第一个会话

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>第二个会话

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>中止最新会话

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>恢复最近中断的会话

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>将驱动器添加到最新的会话

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>后续步骤

* [为导入作业准备硬盘驱动器的示例工作流](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)

