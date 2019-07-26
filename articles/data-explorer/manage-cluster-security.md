---
title: 保护 Azure 中的群集数据资源管理器
description: 本文介绍如何在 Azure 门户中保护 Azure 数据资源管理器中的群集。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: fbc5b18de093b2c91b17fa310c08a5b02b113a22
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406517"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>保护 Azure 中的群集数据资源管理器

[Azure 磁盘加密](/azure/security/azure-security-disk-encryption-overview)有助于保护和保护数据, 以满足组织的安全性和符合性承诺。 它为群集虚拟机的 OS 和数据磁盘提供卷加密。 它还与[Azure Key Vault](/azure/key-vault/)集成, 以帮助你控制和管理磁盘加密密钥和机密, 并确保 VM 磁盘上的所有数据在 Azure 存储中静态加密。 群集安全设置允许在群集上启用磁盘加密。
  
## <a name="enable-encryption-at-rest"></a>启用静态加密
  
启用群集上[的静态加密](/azure/security/azure-security-encryption-atrest)可为存储的数据 (静态) 提供数据保护。 

1. 在 Azure 门户中转到 Azure 数据资源管理器群集资源。 在 "**设置**" 标题下, 选择 "**安全性**"。 

    ![启用静态加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在 "**安全**" 窗口中, 为 "**磁盘加密**安全设置" 选择 **"打开**"。 

1. 选择**保存**。

## <a name="next-steps"></a>后续步骤

[检查群集运行状况](/azure/data-explorer/check-cluster-health)
