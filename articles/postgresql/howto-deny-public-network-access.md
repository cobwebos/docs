---
title: 拒绝公共网络访问-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 了解如何对 Azure Database for PostgreSQL 单一服务器使用 Azure 门户配置拒绝公共网络访问
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375117"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>使用 Azure 门户 Azure Database for PostgreSQL 单个服务器拒绝公共网络访问

本文介绍如何将 Azure Database for PostgreSQL 单个服务器配置为拒绝所有公共配置，并仅允许通过专用终结点进行的连接，从而进一步增强网络安全。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [单台服务器 Azure Database for PostgreSQL](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>设置拒绝公共网络访问

按照以下步骤设置 PostgreSQL 单一服务器拒绝公共网络访问：

1. 在[Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for PostgreSQL 单一服务器。

1. 在 "PostgreSQL 单一服务器" 页上的 "**设置**" 下，单击 "**连接安全性**" 以打开 "连接安全配置" 页。

1. 在 "**拒绝公共网络访问**" 中，选择 **"是"** 以对 PostgreSQL 的单一服务器启用拒绝公共访问。

    ![Azure Database for PostgreSQL 单一服务器拒绝网络访问](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. 单击 **“确定”** ，保存这些更改。

1. 通知会确认已成功启用连接安全设置。

    ![Azure Database for PostgreSQL 单一服务器拒绝网络访问成功](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-on-metric.md)。