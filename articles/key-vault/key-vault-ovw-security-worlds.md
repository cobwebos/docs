---
ms.assetid: 
title: "Azure Key Vault 安全体系 | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6477e8d91b71361ef91763418b22596deb216f02
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault 安全体系和地理边界

Azure Key Vault 是一项多租户服务，在每个 Azure 位置使用一个硬件安全模块 (Hsm) 池。 

同一地理区域中 Azure 位置的所有 HSM 都共享相同的加密边界（Thales 安全体系）。 例如，美国东部和美国西部共享相同的安全体系，因为它们都属于地理位置美国。 同样，位于日本的所有 Azure 位置共享相同的安全体系，位于澳大利亚、印度等地理位置的所有 Azure 位置也是如此。 

只要满足以下两个条件，从某一 Azure 位置中的 Key Vault 对密钥所执行的备份可以还原到另一 Azure 位置中的 Key Vault：

- 两个 Azure 位置属于相同的地理位置
- 两个 Key Vault 属于同一个 Azure 订阅

例如，在印度西部的 Key Vault 中由给定订阅对密钥执行的备份，只能还原到相同订阅和地理位置（印度西部、印度中部或印度南部）中的另一个 Key Vault。 



