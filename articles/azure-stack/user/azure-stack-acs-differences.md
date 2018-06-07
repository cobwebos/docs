---
title: Azure 堆栈存储差异和注意事项 |Microsoft 文档
description: 了解 Azure 堆栈存储和 Azure 存储，以及 Azure 堆栈部署注意事项之间的差异。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviwer: xiaofmao
ms.openlocfilehash: 2a6cb3f1a1f8009af411ba4d97a23194f6f089ae
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604454"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure 堆栈存储： 差异和注意事项

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈存储是一组存储在 Microsoft Azure 堆栈中的云服务。 Azure 堆栈存储提供了 blob、 表、 队列和帐户管理功能，带有 Azure 一致的语义。

本文汇总了 Azure Stack 存储与 Azure 存储服务之间的已知差异。 它还列出了部署 Azure Stack 时要考虑的事项。 若要了解有关高级全局 Azure 和 Azure 堆栈之间的差异信息，请参阅[密钥注意事项](azure-stack-considerations.md)主题。

## <a name="cheat-sheet-storage-differences"></a>速查表：存储差异

| 功能 | Azure（公有云） | Azure Stack |
| --- | --- | --- |
|文件存储|支持基于云的 SMB 文件共享|尚不支持
|Azure 存储服务加密对静止数据|256 位 AES 加密|BitLocker 128 位 AES 加密
|存储帐户类型|通用和 Azure blob 存储帐户|仅限常规用途。
|复制选项|本地冗余存储、异地冗余存储、读取访问异地冗余存储和区域冗余存储|本地冗余存储。
|高级存储|完全支持|可预配，但无性能限制或保证。
|托管磁盘|支持高级和标准版|尚不支持。
|Blob 名称|1,024 个字符（2,048 字节）|880 个字符（1,760 字节）
|块 Blob 大小上限|4.75 TB（100 MB X 50,000 块）|4.75 TB（100 MB x 50,000 块），适用于 1802 更新或更高版本。 50,000 X 4 MB（约 195 GB），适用于以前的版本。
|页 Blob 快照复制|支持备份已附加到运行中 VM 的 Azure 非托管 VM 磁盘|尚不支持。
|页 Blob 增量快照复制|支持高级和标准 Azure 页 Blob|尚不支持。
|适用于 Blob 存储的存储层|热存储层、冷存储层和存档存储层。|尚不支持。
适用于 Blob 存储的软删除|预览|尚不支持。
|页 Blob 大小上限|8 TB|1 TB
|页 Blob 页面大小|512 字节|4 KB
|表分区键和行键大小|1,024 个字符（2,048 字节）|400 个字符（800 字节）
|Blob 快照|一个 blob 的最大快照数不受限制。|一个 blob 的最大快照数为 1,000。|

存储指标也有一些差异：

* 存储指标中的事务数据不区分内部或外部网络带宽。
* 存储指标中的事务数据不包含虚拟机对所装载的驱动器的访问。

## <a name="api-version"></a>API 版本

使用 Azure Stack 存储时支持以下版本：

Azure 存储服务 API：

1802 更新或更高版本：

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

以前的版本：

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure 存储服务管理 API：

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK 版本

Azure 堆栈存储空间支持以下客户端库：

| 客户端库 | Azure Stack 支持的版本 | 链接                                                                                                                                                                                                                                                                                                                                     | 终结点规范       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | 从 6.2.0 到 8.7.0。          | Nuget 包：<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | app.config 文件              |
| Java           | 从 4.1.0 到 6.1.0           | Maven 包：<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | 连接字符串设置      |
| Node.js        | 从 1.1.0 到 2.7.0           | NPM 链接：<br>https://www.npmjs.com/package/azure-storage<br>（例如：运行“npm install azure-storage@2.7.0”）<br> <br>Github 版本：<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | 服务实例声明 |
| C++            | 从 2.4.0 到 3.1.0           | Nuget 包：<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | 连接字符串设置      |
| PHP            | 从 0.15.0 到 1.0.0          | GitHub 版本：<br>https://github.com/Azure/azure-storage-php/releases<br> <br>通过编辑器安装（请参阅下面的详细信息）                                                                                                                                                                                                                  | 连接字符串设置      |
| Python         | 从 0.30.0 到 1.0.0          | GitHub 版本：<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | 服务实例声明 |
| Ruby           | 从 0.12.1 到 1.0.1          | RubyGems 包：<br>常见：<br>https://rubygems.org/gems/azure-storage-common/<br>Blob：https://rubygems.org/gems/azure-storage-blob/<br>队列：https://rubygems.org/gems/azure-storage-queue/<br>表：https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-ruby/releases | 连接字符串设置      |

## <a name="next-steps"></a>后续步骤

* [Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)
* [Azure Stack 存储简介](azure-stack-storage-overview.md)
