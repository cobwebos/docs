---
title: Key Vault 版本
description: 各种版本的 Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-versions"></a>Key Vault 版本

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - 托管存储帐户密钥

2017 年夏 - 新增了存储帐户密钥功能，可更轻松地与 Azure 存储集成。 参阅概述主题[托管存储帐户密钥概述](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)，了解详细信息。

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - 软删除

2017 年夏 - 新增了软删除功能，增强了 Key Vault 和 Key Vault 对象的数据保护。 参阅概述主题[软删除概述](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)，了解详细信息。

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - 证书管理

2016 年 9 月 26 日，2015-06-01 正式版中新增了证书管理功能。

## <a name="2015-06-01---general-availability"></a>2015-06-01 - 正式版

2015 年 6 月 24 日发布了 2015-06-01 正式版。

此版本进行了以下更改：

- 删除密钥 - 删除了“使用”字段。
- 获取有关密钥的信息 - 删除了“使用”字段。
- 将密钥导入保管库 - 删除了“使用”字段。
- 还原密钥 - 删除了“使用”字段。
- 将“RSA_OAEP”改为RSA 算法的“RSA-OAEP”。 请参阅[关于密钥、机密和证书](about-keys-secrets-and-certificates.md)。

## <a name="2015-02-01-preview"></a>2015-02-01-预览版 

2015 年 4 月 20 日发布了第二版 2015-02-01-预览版。 有关详细信息，请参阅 [REST API 更新](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) 博客文章。

更新了以下任务：

- 列出保管库中的密钥 - 向操作添加了分页支持。
- 列出密钥版本 - 添加了列出密钥版本的操作。
- 列出保管库中的机密 - 添加了分页支持。
- 列出机密版本 - 添加了列出机密版本的操作。
- 所有操作 - 添加了属性创建/更新时间戳。
- 创建机密 - 向机密添加了 Content-Type。
- 创建密钥 - 添加了标记作为可选信息。
- 创建机密 - 添加了标记作为可选信息。
- 更新密钥 - 添加了标记作为可选信息。
- 更新机密 - 添加了标记作为可选信息。
- 将机密的最大大小从 10 KB 更改为 25 KB。 请参阅[关于密钥、机密和证书](about-keys-secrets-and-certificates.md)。

## <a name="2014-12-08-preview"></a>2014-12-08-预览版

2015 年 1 月 8 日发布了第一版 2014-12-08-预览版。

## <a name="see-also"></a>另请参阅
- [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
