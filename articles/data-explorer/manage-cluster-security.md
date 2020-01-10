---
title: 保护 Azure 中的群集数据资源管理器
description: 本文介绍如何在 Azure 门户中保护 Azure 数据资源管理器中的群集。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720338"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>保护 Azure 中的群集数据资源管理器-Azure 门户

[Azure 磁盘加密](/azure/security/azure-security-disk-encryption-overview)有助于保护和保护数据，以满足组织的安全性和符合性承诺。 它为群集虚拟机的 OS 和数据磁盘提供卷加密。 它还与[Azure Key Vault](/azure/key-vault/)集成，使我们能够控制和管理磁盘加密密钥和机密，并确保 VM 磁盘上的所有数据都已加密。 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>在 Azure 门户中启用静态加密
  
群集安全设置允许在群集上启用磁盘加密。 启用群集上[的静态加密](/azure/security/fundamentals/encryption-atrest)可为存储的数据（静态）提供数据保护。 

1. 在 Azure 门户中转到 Azure 数据资源管理器群集资源。 在 "**设置**" 标题下，选择 "**安全性**"。 

    ![启用静态加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在 "**安全**" 窗口中，为 "**磁盘加密**安全设置" 选择 **"打开**"。 

1. 选择“保存”。
 
> [!NOTE]
> 选择 "**关闭**" 以在启用加密后禁用加密。

## <a name="next-steps"></a>后续步骤

[检查群集运行状况](/azure/data-explorer/check-cluster-health)
