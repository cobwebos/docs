---
title: Azure Key Vault 将保管库移动到不同的区域 |Microsoft Docs
description: 有关将密钥保管库移动到不同区域的指南。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254143"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>跨区域移动 Azure Key Vault

## <a name="overview"></a>概述

Key Vault 不支持允许将密钥保管库移到另一个区域的资源移动操作。 如果你的业务需要将一个密钥保管库移到另一个区域，本文将介绍解决方法。 每个选项都有限制，因此在生产环境中尝试之前，必须先了解这些解决方法的含义。

如果需要将 key vault 移到另一个区域，解决方案是在所需的区域中创建新的密钥保管库，并手动将现有密钥保管库中的每个机密复制到新的密钥保管库。 此操作可通过以下列出的任一方式完成：

## <a name="design-considerations"></a>设计注意事项

* Key Vault 名称是全局唯一的。 你将不能重复使用相同的保管库名称。

* 你将需要在新的密钥保管库中重新配置访问策略和网络配置设置。

* 你将需要在新的密钥保管库中重新配置软删除和清除保护。

* 备份和还原操作不会保留 autorotation 设置，你可能需要重新配置这些设置。

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>选项 1-使用密钥保管库备份和还原命令

可以使用 backup 命令备份保管库中的每个机密、密钥和证书。 你的机密将下载为加密的 blob。 然后，可以将 blob 还原到新的密钥保管库。 下面的链接中介绍了这些命令。

[Azure Key Vault 命令](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>限制

* 你无法在一个地理位置备份 key vault，并将其还原到另一个地理区域。 详细了解 Azure 地理位置。 [链接](https://azure.microsoft.com/global-infrastructure/geographies/)。

* Backup 命令备份每个机密的所有版本。 如果你有一个具有大量先前版本（大于10）的机密，则可能是请求超出了允许的最大请求大小，操作可能会失败。

## <a name="option-2---manually-download-and-upload-secrets"></a>选项 2-手动下载和上传机密

可以手动下载某些机密类型。 例如，你可以将证书作为 .pfx 文件下载。 此选项可消除某些机密类型（如证书）的地理限制。 可以将 .pfx 文件上传到任何区域中的任何密钥保管库。 将以非密码保护的格式下载机密。 当执行移动时，你将负责保护机密 Key Vault。
