---
title: 拒绝公共网络访问-Azure 门户-Azure Database for MariaDB
description: 了解如何使用 Azure 门户为 Azure Database for MariaDB 配置拒绝公共网络访问
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: f33dbfa0b96d7f6d85443005ff7e8b1a780c75a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86104378"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>使用 Azure 门户拒绝 Azure Database for MariaDB 中的公共网络访问

本文介绍如何将 Azure Database for MariaDB 服务器配置为拒绝所有公共配置，并仅允许通过专用终结点进行的连接，从而进一步增强网络安全。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>设置拒绝公共网络访问

请按照以下步骤设置 MariaDB 服务器拒绝公共网络访问：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MariaDB 服务器。

1. 在 MariaDB 服务器页上的“设置”下，单击“连接安全性”，打开连接安全性配置页 。

1. 在 "拒绝公共网络访问" 中，选择 **"是"** 以对 MariaDB 服务器启用拒绝公共访问。

    ![Azure Database for MariaDB 拒绝网络访问](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. 单击“保存”以保存更改。

1. 此时将显示一则通知，确认已成功启用了连接安全性设置。

    ![Azure Database for MariaDB 拒绝网络访问成功](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>后续步骤

了解 [如何创建有关指标的警报](howto-alert-metric.md)。