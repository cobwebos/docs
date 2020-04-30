---
title: FarmBeats 的灾难恢复
description: 本文介绍数据恢复如何防止丢失数据。
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683898"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats 的灾难恢复

数据恢复可防止在 Azure 区域折叠等事件中丢失数据。 在这种情况下，你可以开始故障转移，并恢复 FarmBeats 部署中存储的数据。

数据恢复不是 Azure FarmBeats 中的默认功能。 可以通过配置 FarmBeats 用来将数据存储在 Azure 配对区域中的所需 Azure 资源，手动配置此功能。 使用主动-被动方法启用恢复。

以下部分提供有关如何在 Azure FarmBeats 中配置数据恢复的信息：

- [启用数据冗余](#enable-data-redundancy)
- [从联机备份还原服务](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>启用数据冗余

FarmBeats 将数据存储在三个 Azure 第一方服务中，即**azure 存储**、 **Cosmos DB**和**时序见解**。 使用以下步骤将这些服务的数据冗余启用到配对的 Azure 区域：

1.  **Azure 存储**-按照此准则为 FarmBeats 部署中的每个存储帐户启用数据冗余。
2.  **Azure Cosmos DB** -按照此原则为你的 FarmBeats 部署的 Cosmos DB 帐户启用数据冗余。
3.  **Azure 时序见解（TSI）** -TSI 当前不提供数据冗余。 若要恢复时序见解数据，请前往传感器/天气合作伙伴，并再次将数据推送到 FarmBeats 部署。

## <a name="restore-service-from-online-backup"></a>从联机备份还原服务

你可以为 FarmBeats 部署启动为其存储的故障转移和恢复存储的数据。 恢复 Azure 存储和 Cosmos DB 的数据后，在 Azure 配对区域中创建另一个 FarmBeats 部署，然后将新部署配置为使用还原的数据存储中的数据（即 Azure 存储和 Cosmos DB），方法是使用以下步骤：

1. [配置 Cosmos DB](#configure-cosmos-db)
2. [配置存储帐户](#configure-storage-account)


### <a name="configure-cosmos-db"></a>配置 Cosmos DB

复制还原的 Cosmos DB 的访问密钥并更新新的 FarmBeats Datahub Key Vault。


  ![灾难恢复](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 复制已还原 Cosmos DB 的 URL，并将其更新为新的 FarmBeats Datahub 应用服务配置。 你现在可以在新的 FarmBeats 部署中删除 Cosmos DB 帐户。

  ![灾难恢复](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>配置存储帐户

复制还原的存储帐户的访问密钥，并在新的 FarmBeats Datahub Key Vault 中更新该密钥。

![灾难恢复](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 请确保在新的 FarmBeats Batch VM 配置文件中更新存储帐户名称。

![灾难恢复](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

同样，如果已为加速器存储帐户启用数据恢复，请按照步骤2在新的 FarmBeats 实例中更新加速器存储帐户访问密钥和名称。
