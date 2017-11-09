---
title: "Azure 堆栈存储： 差异和注意事项"
description: "了解 Azure 堆栈部署注意事项以及 Azure 堆栈存储和 Azure 存储空间之间的差异。"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 4d6fb44fd6fd2261059ca45093d8b49345adfa74
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure 堆栈存储： 差异和注意事项

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈存储是存储在 Microsoft Azure 堆栈中的云服务的集合。 Azure 堆栈存储提供了 blob、 表、 队列和帐户管理功能，带有 Azure 一致的语义。

本文总结了从 Azure 存储的已知的 Azure 堆栈存储差异。 它还概述了部署 Azure 堆栈时，需要注意的事项。 若要了解有关高级 Azure 堆栈和 Azure 之间的差异信息，请参阅[密钥注意事项](azure-stack-considerations.md)主题。

## <a name="cheat-sheet-storage-differences"></a>速查表： 存储差异

| 功能 | Azure （全局） | Azure Stack |
| --- | --- | --- |
|文件存储|基于云的支持 SMB 文件共享|尚不支持
|在 rest 加密的数据|256 位 AES 加密|尚不支持
|存储帐户类型|通用和 Azure Blob 存储帐户|通用仅
|复制选项|本地冗余存储、 地域冗余存储、 读取访问地域冗余存储和区域冗余存储|本地冗余存储
|高级存储|完全支持|可以设置，但没有性能限制或保证
|托管磁盘|高级和标准支持|尚不支持
|Blob 名称|1024 个字符 （为 2048 个字节）|880 字符 （1,760 字节）
|块 blob 最大大小|4.75 TB （100 MB X 50000 块）|50,000 X 4 MB（约 195 GB）
|页 blob 快照复制|备份 Azure 非托管的 VM 磁盘附加到正在运行支持的 VM|尚不支持
|页 blob 增量快照复制|高级和标准 Azure 页 blob 支持|尚不支持
|页 blob 最大大小|8 TB|1 TB
|页 blob 页大小|512 字节|4 KB
|表分区键和行键大小|1024 个字符 （为 2048 个字节）|400 个字符 （800 字节为单位）

### <a name="metrics"></a>度量值
还有一些与存储度量值的差异：
* 存储度量值中的事务数据不区分内部或外部的网络带宽。
* 存储度量值中的事务数据不包括与装入的磁盘的虚拟机访问。

## <a name="api-version"></a>API 版本
对 Azure 堆栈存储空间支持以下版本：

* Azure 存储数据服务： [2015年-04-05 REST API 版本](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Azure 存储管理服务： [2015年-05-01-preview、 2015年-06-15 和 2016年-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>后续步骤

* [开始使用 Azure 堆栈存储开发工具](azure-stack-storage-dev.md)
* [Azure 堆栈存储空间简介](azure-stack-storage-overview.md)

