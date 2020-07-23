---
title: 将密钥保管库移动到不同的区域 - Azure 密钥保管库 | Microsoft Docs
description: 本文提供有关将密钥保管库移动到不同区域的指南。
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
ms.openlocfilehash: 4f9f43b3d0aa0af8696300933c08c140951e5e52
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651222"
---
# <a name="move-an-azure-key-vault-across-regions"></a>跨区域移动 Azure 密钥保管库

Azure 密钥保管库不支持资源移动操作，使用这个操作可以将密钥保管库从一个区域移动到另一个区域。 有些组织具有将密钥保管库移动到另一个区域的业务需求，本文提供了适用的解决方法。 每个解决方法选项都存在限制。 在尝试将解决方法应用于生产环境之前，请务必先了解这些解决方法的含义。

若要将密钥保管库移动到另一个区域，请在目标区域中创建一个密钥保管库，然后将现有密钥保管库中的每个机密分别手动复制到新的密钥保管库中。 可以使用以下两个选项之一来执行此操作。

## <a name="design-considerations"></a>设计注意事项

在开始之前，请注意理解以下概念：

* 密钥保管库名称具备全局唯一性。 不能重复使用保管库名称。
* 需要在新的密钥保管库中重新配置访问策略和网络配置设置。
* 需要在新的密钥保管库中重新配置软删除和清除保护。
* 备份和还原操作不会保留自动轮换设置。 你可能需要重新配置这些设置。

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>选项 1：使用密钥保管库备份和还原命令

可以使用备份命令来备份保管库中的每个机密、密钥和证书。 下载的机密是加密形式的 blob。 然后，可以将 blob 还原到新的密钥保管库中。 有关命令的列表，请参阅 [Azure 密钥保管库命令](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)。

使用备份和还原命令存在两个限制：

* 不能在一个地理位置备份密钥保管库，并将其还原到另一个地理位置。 有关详细信息，请参阅[Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)。

* 备份命令可备份每个机密的所有版本。 如果你的机密具有大量早期版本（超过 10 个），请求大小可能超出允许的最大值，并且操作可能会失败。

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>选项 2：手动下载和上传密钥保管库机密

可以手动下载某些机密类型。 例如，可以将证书下载为 PFX 文件。 如果使用此选项，那么某些机密类型（如证书）不再具有地理位置方面的限制。 可以将 PFX 文件上传到任何区域中的任何密钥保管库。 以非密码保护的格式下载机密。 在移动过程中，需要负责保证机密的安全性。
