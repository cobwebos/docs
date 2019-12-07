---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895293"
---
Azure 存储加密静态存储帐户中的所有数据。 默认情况下，使用 Microsoft 托管的密钥对数据进行加密。 为了更进一步控制加密密钥，你可以提供客户管理的密钥，以用于加密 blob 和文件数据。

客户管理的密钥必须存储在 Azure Key Vault 中。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure 存储加密和密钥管理的详细信息，请参阅[静态数据的 Azure 存储加密](../articles/storage/common/storage-service-encryption.md)。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../articles/key-vault/key-vault-overview.md)
