---
title: 使用密钥保管库的最佳做法 - Azure 密钥保管库 |微软文档
description: 本文档介绍了一些使用密钥保管库的最佳做法
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270987"
---
# <a name="best-practices-to-use-key-vault"></a>使用密钥保管库的最佳做法

## <a name="control-access-to-your-vault"></a>控制对保管库的访问权限

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 因为此数据是敏感数据和业务关键数据，所以需要保护对密钥保管库的访问，只允许得到授权的应用程序和用户进行访问。 [本文](key-vault-secure-your-key-vault.md)概述了密钥保管库访问模型。 其中介绍了身份验证和授权，以及如何保护对密钥保管库的访问。

控制对保管库的访问权限的建议如下：
1. 锁定对订阅、资源组和密钥保管库 (RBAC) 的访问权限
2. 为每个保管库创建访问策略
3. 使用最低特权访问主体授予访问权限
4. 打开防火墙和 [VNET 服务终结点](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>使用单独的密钥保管库

我们的建议是对每个环境（开发环境、预生产环境和生产环境）的每个应用程序使用一个保管库。 这可以帮助你避免在不同环境之间共享机密，并可在出现安全漏洞时降低威胁。

## <a name="backup"></a>备份

确保在保管库中更新/删除/创建对象时定期执行[保管库](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/)的备份。

## <a name="turn-on-logging"></a>打开日志记录

[打开](key-vault-logging.md)保管库的日志记录。 还设置警报。

## <a name="turn-on-recovery-options"></a>启用恢复选项

1. 启用[软删除](key-vault-ovw-soft-delete.md)。
2. 如果要防止强制删除机密/保管库，即使启用软删除后，也要启用清除保护。
