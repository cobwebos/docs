---
title: 服务器场灾难恢复
description: 本文介绍了数据恢复如何防止数据丢失。
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313069"
---
# <a name="disaster-recovery-for-farmbeats"></a>服务器场灾难恢复

数据恢复可保护您在 Azure 区域崩溃等事件中丢失数据。 在这种情况下，您可以启动故障转移，并恢复存储在服务器场Beats部署中的数据。

数据恢复不是 Azure FarmBeats 中的默认功能。 可以通过配置 FarmBeats 用于在 Azure 配对区域中存储数据所需的 Azure 资源来手动配置此功能。 使用主动 + 被动方法启用恢复。

以下各节提供有关如何在 Azure FarmBeats 中配置数据恢复的信息：

- [启用数据冗余](#enable-data-redundancy)
- [从联机备份恢复服务](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>启用数据冗余

FarmBeats 将数据存储在三个 Azure 第一方服务中，即**Azure 存储**、**宇宙 DB**和**时间序列见解**。 使用以下步骤将这些服务的数据冗余启用到配对的 Azure 区域：

1.  **Azure 存储**- 遵循此准则，为服务器场 Bbeats 部署中的每个存储帐户启用数据冗余。
2.  **Azure Cosmos DB** - 遵循此准则，为服务器场节拍部署的 Cosmos DB 帐户启用数据冗余。
3.  **Azure 时间序列见解 （TSI）** - TSI 当前不提供数据冗余。 要恢复时间序列见解数据，请转到传感器/天气合作伙伴，并将数据再次推送到 FarmBeats 部署。

## <a name="restore-service-from-online-backup"></a>从联机备份恢复服务

您可以启动故障转移并恢复存储的数据，这些数据存储用于 FarmBeats 部署。 恢复 Azure 存储和 Cosmos DB 的数据后，在 Azure 配对区域中创建另一个服务器场Beats部署，然后使用以下步骤配置新部署以使用还原的数据存储（即 Azure 存储和 Cosmos DB）中的数据：

1. [配置 Cosmos DB](#configure-cosmos-db)
2. [配置存储帐户](#configure-storage-account)


### <a name="configure-cosmos-db"></a>配置 Cosmos DB

复制还原的 Cosmos DB 的访问密钥并更新新的 FarmBeats Datahub 密钥保管库。


  ![灾难恢复](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 复制还原的 Cosmos DB 的 URL 并在新的服务器场节拍 Datahub 应用服务配置中更新它。 您现在可以在新的服务器场节拍部署中删除 Cosmos DB 帐户。

  ![灾难恢复](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>配置存储帐户

复制还原的存储帐户的访问密钥，并在新的 FarmBeats Datahub 密钥保管库中更新它。

![灾难恢复](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 确保在新的服务器场节拍批处理 VM 配置文件中更新存储帐户名称。

![灾难恢复](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

同样，如果已为加速器存储帐户启用数据恢复，请按照步骤 2 更新新的 FarmBeats 实例中的加速器存储帐户访问密钥和名称。
