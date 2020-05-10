---
title: 连接到 Dynamics 365
description: 使用 Azure 逻辑应用创建和管理 Dynamics 365 记录
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994292"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Dynamics 365 中创建和管理记录

Dynamics 365 使用[Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)。 对于到 Dynamics 365 的连接，请使用[Common Data Service 连接器](https://docs.microsoft.com/connectors/commondataservice/)。

> [!IMPORTANT]
> [Dynamics 365 连接器](https://docs.microsoft.com/connectors/dynamicscrmonline/)已弃用，但在删除之前将继续工作。 不要将 Dynamics 365 连接器用于新的逻辑应用。 尚未为删除 Dynamics 365 连接器发布时间线。 不需要将现有的逻辑应用转换为 Common Data Service 连接器或其他计划的新连接器，但在删除连接器时需要转换逻辑应用。 有关详细信息，请参阅[Dynamics 365 连接器已弃用](https://docs.microsoft.com/power-platform/important-changes-coming)。
>
> [Common Data Service 连接器](https://docs.microsoft.com/connectors/commondataservice/)提供与弃用的 Dynamics 365 连接器相同的功能，但包含提高可靠性的改进。 有关在逻辑应用中使用 Common Data Service 连接器的信息，请参阅[使用 Azure 逻辑应用创建和管理 Common Data Service 记录](../connectors/connect-common-data-service.md)。

有关 Common Data Service 的详细信息，请参阅以下主题：

* [了解： Common Data Service 入门](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [了解：通过使用电源平台和 Common Data Service 来连接和分析 Dynamics 365 数据](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)