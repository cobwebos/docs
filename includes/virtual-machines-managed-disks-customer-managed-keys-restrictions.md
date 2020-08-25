---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f45ecf9985f17008ce7f751b21a04deefc285461
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88809832"
---
- 仅支持2048位、3072位和4096位大小的 [软件和 HSM RSA 密钥](../articles/key-vault/keys/about-keys.md) ，无其他密钥或大小。
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) 密钥需要 Azure 密钥保管库的 **高级** 层。
- 从使用服务器端加密和客户托管密钥加密的自定义映像创建的磁盘必须使用相同的客户托管密钥进行加密，且必须位于同一订阅中。
- 从使用服务器端加密和客户托管密钥加密的磁盘创建的快照必须使用相同的客户托管密钥进行加密。
- 与客户托管密钥相关的所有资源（Azure Key Vault、磁盘加密集、VM、磁盘和快照）都必须位于同一订阅和区域中。
- 使用客户托管密钥加密的磁盘、快照和映像不能移至另一个订阅。
- 当前或以前使用 Azure 磁盘加密加密的托管磁盘不能使用客户托管的密钥进行加密。
- 每个订阅最多只能为每个区域创建50磁盘加密集。
- 有关将客户托管密钥与共享映像库结合使用的信息，请参阅[预览版：使用客户托管密钥加密映像](../articles/virtual-machines/image-version-encryption.md)。
