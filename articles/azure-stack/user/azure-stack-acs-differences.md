---
title: "Azure 堆栈存储： 差异和注意事项"
description: "了解 Azure 堆栈部署注意事项以及 Azure 堆栈存储和 Azure 存储空间之间的差异。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 7c4f030018f388302c3b60a41086bbd97c86513d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure 堆栈存储： 差异和注意事项

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈存储是存储在 Microsoft Azure 堆栈中的云服务的集合。 Azure 堆栈存储提供了 blob、 表、 队列和帐户管理功能，带有 Azure 一致的语义。

本文总结了从 Azure 存储的已知的 Azure 堆栈存储差异。 它还概述了部署 Azure 堆栈时，需要注意的事项。 若要了解有关高级 Azure 堆栈和 Azure 之间的差异信息，请参阅[密钥注意事项](azure-stack-considerations.md)主题。

## <a name="cheat-sheet-storage-differences"></a>速查表： 存储差异

| 功能 | Azure （全局） | Azure Stack |
| --- | --- | --- |
|文件存储|基于云的支持 SMB 文件共享|尚不支持
|静态数据的 Azure 存储服务加密|256 位 AES 加密|BitLocker 128 位 AES 加密
|存储帐户类型|通用和 Azure Blob 存储帐户|通用仅。
|复制选项|本地冗余存储、 地域冗余存储、 读取访问地域冗余存储和区域冗余存储|本地冗余存储。
|高级存储|完全支持|可以设置，但没有性能限制或保证。
|托管磁盘|高级和标准支持|尚不支持。
|Blob 名称|1024 个字符 （为 2048 个字节）|880 字符 （1,760 字节）
|块 blob 最大大小|4.75 TB （100 MB X 50000 块）|1802 更新或较新版本的 4.75 TB （100 MB x 50000 块）。 对于早期版本的 50,000 x 4 MB (约 195 GB)。
|页 blob 快照复制|备份 Azure 非托管的 VM 磁盘附加到正在运行支持的 VM|尚不支持。
|页 blob 增量快照复制|高级和标准 Azure 页 blob 支持|尚不支持。
|Blob 存储的存储层|热，哦，和存档存储层。|尚不支持。
Blob 存储的的软删除|预览|尚不支持。
|页 blob 最大大小|8 TB|1 TB
|页 blob 页大小|512 字节|4 KB
|表分区键和行键大小|1024 个字符 （为 2048 个字节）|400 个字符 （800 字节为单位）

### <a name="metrics"></a>度量值
还有一些与存储度量值的差异：
* 存储度量值中的事务数据不区分内部或外部的网络带宽。
* 存储度量值中的事务数据不包括与装入的磁盘的虚拟机访问。

## <a name="api-version"></a>API 版本
对 Azure 堆栈存储空间支持以下版本：

Azure 存储服务 Api:

1802 更新或更高版本：
 - [2017-04-17](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)

以前的版本：
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)


Azure 存储服务管理 Api:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK 版本

对 Azure 堆栈存储空间支持以下客户端库：

| 客户端库 | Azure 堆栈支持版本 | 链接                                                                                                                                                                                                                                                                                                                                     | 终结点规范       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | 从到 8.7.0 6.2.0。          | Nuget 包：<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | app.config 文件              |
| Java           | 从到 6.1.0 4.1.0           | Maven 包：<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | 连接字符串设置      |
| Node.js        | 从到 2.7.0 1.1.0           | NPM 链接：<br>https://www.npmjs.com/package/azure-storage<br>(例如： 运行"npm 安装azure-storage@2.7.0")<br> <br>Github 版本：<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | 服务实例声明 |
| C++            | 从 3.1.0 到 2.4.0           | Nuget 包：<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | 连接字符串设置      |
| PHP            | 从 0.15.0 到 1.0.0          | GitHub 版本：<br>https://github.com/Azure/azure-storage-php/releases<br> <br>通过 Composer 安装 （请参阅下面的详细信息）                                                                                                                                                                                                                  | 连接字符串设置      |
| Python         | 从 0.30.0 到 1.0.0          | GitHub 版本：<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | 服务实例声明 |
| Ruby           | 从到 1.0.1 0.12.1          | RubyGems 包：<br>常见：<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>队列： https://rubygems.org/gems/azure-storage-queue/<br>表： https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub 版本：<br>https://github.com/Azure/azure-storage-ruby/releases | 连接字符串设置      |

## <a name="next-steps"></a>后续步骤

* [开始使用 Azure 堆栈存储开发工具](azure-stack-storage-dev.md)
* [Azure 堆栈存储空间简介](azure-stack-storage-overview.md)

