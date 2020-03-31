---
title: 拒绝公共网络访问 - Azure 门户 - MySQL 的 Azure 数据库
description: 了解如何使用 Azure 数据库的 Azure 数据库为 MySQL 配置拒绝公共网络访问
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374948"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户拒绝 Azure 数据库中的公用网络访问

本文介绍如何为 MySQL 服务器配置 Azure 数据库以拒绝所有公共配置，并只允许通过专用终结点进行连接以进一步增强网络安全。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [MySQL 的 Azure 数据库](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>设置拒绝公共网络访问

按照以下步骤设置 MySQL 服务器拒绝公共网络访问：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 MySQL 服务器页上，在 **"设置"** 下，单击 **"连接安全**"以打开连接安全配置页。

1. 在 **"拒绝公共网络访问"** 中，选择 **"是**"以启用 MySQL 服务器的拒绝公共访问。

    ![用于 MySQL 拒绝网络访问的 Azure 数据库](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. 单击 **“确定”**，保存这些更改。

1. 通知将确认连接安全设置已成功启用。

    ![用于 MySQL 拒绝网络访问成功的 Azure 数据库](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。