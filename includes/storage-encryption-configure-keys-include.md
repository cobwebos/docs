---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115428"
---
Azure 存储使用 Microsoft 托管的密钥或客户托管密钥支持静态加密。 客户托管密钥，可以创建、 轮换、 禁用和撤消访问控制。

使用 Azure 密钥保管库来管理你的密钥和审核密钥用法。 可以创建自己的密钥并将其存储在密钥保管库，或使用 Azure 密钥保管库 Api 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../articles/key-vault/key-vault-overview.md)
