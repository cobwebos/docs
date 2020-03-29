---
title: 拒绝公共网络访问 - Azure 门户 - 后数据库的 Azure 数据库 - 单个服务器
description: 了解如何为 PostgreSQL 单一服务器使用 Azure 数据库的 Azure 数据库配置拒绝公共网络访问
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375117"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>使用 Azure 门户拒绝 Azure 数据库中使用 Azure 门户的 PostgreSQL 单一服务器的公共网络访问

本文介绍如何为 PostgreSQL Single 服务器配置 Azure 数据库以拒绝所有公共配置，并只允许通过专用终结点进行连接以进一步增强网络安全。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [后 SQL 单个服务器的 Azure 数据库](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>设置拒绝公共网络访问

按照以下步骤设置 PostgreSQL 单个服务器拒绝公共网络访问：

1. 在[Azure 门户](https://portal.azure.com/)中，为 PostgreSQL 单个服务器选择现有的 Azure 数据库。

1. 在 PostgreSQL 单个服务器页上，单击"**设置"** 下，单击 **"连接安全**"以打开连接安全配置页。

1. 在 **"拒绝公共网络访问**"中，选择 **"是**"以启用 PostgreSQL 单服务器的拒绝公共访问。

    ![用于 PostgreSQL 单一服务器拒绝网络访问的 Azure 数据库](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. 单击 **“确定”**，保存这些更改。

1. 通知将确认连接安全设置已成功启用。

    ![用于 PostgreSQL 单一服务器的 Azure 数据库 拒绝网络访问成功](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。