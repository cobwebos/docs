---
title: azcopy make | Microsoft Docs
description: 本文提供有关 azcopy make 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ad64d56899a9a54f4c8aede78ba6b4f2b77b8249
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331533"
---
# <a name="azcopy-make"></a>azcopy make

创建容器或文件共享。

## <a name="synopsis"></a>概要

创建由给定资源 URL 表示的容器或文件共享。

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>选项

|选项|说明|
|--|--|
|-h、--help|显示 make 命令的帮助内容。 |
|--quota-gb uint32|指定共享的最大大小（以千兆字节 (GB) 为单位），0 表示接受文件服务的默认配额。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps 浮点数|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   |指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
