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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895293"
---
Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了更进一步控制加密密钥，可以提供客户管理的密钥，以用于对 Blob 和文件数据进行加密。

客户管理的密钥必须存储在 Azure密钥保管库中。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure 存储加密和密钥管理的详细信息，请参阅[静态数据的 Azure 存储加密](../articles/storage/common/storage-service-encryption.md)。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../articles/key-vault/key-vault-overview.md)
