---
title: 拒绝公共网络访问-Azure 门户-Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 配置拒绝公共网络访问
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: f8156b01244012d78214f2ba8c49ed76dbceed6d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118777"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户拒绝 Azure Database for MySQL 中的公共网络访问

本文介绍如何将 Azure Database for MySQL 服务器配置为拒绝所有公共配置，并仅允许通过专用终结点进行的连接，从而进一步增强网络安全。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>设置拒绝公共网络访问

请按照以下步骤设置 MySQL 服务器拒绝公共网络访问权限：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 "MySQL 服务器" 页上的 "**设置**" 下，单击 "**连接安全性**" 以打开 "连接安全配置" 页。

1. 在 "**拒绝公共网络访问**" 中，选择 **"是"** 为你的 MySQL 服务器启用拒绝公共访问。

    ![Azure Database for MySQL 拒绝网络访问](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. 单击“保存”以保存更改。

1. 通知会确认已成功启用连接安全设置。

    ![Azure Database for MySQL 拒绝网络访问成功](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。