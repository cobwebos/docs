---
title: TLS 配置-Azure 门户-Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 设置 TLS 配置
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375286"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 中配置 TLS 设置

本文介绍如何配置 Azure Database for MySQL 服务器，使其强制执行最低 TLS 版本的连接，并拒绝所有 TLS 版本较低的连接，从而提高网络安全性。

客户现在能够强制 TLS 版本连接到其 Azure Database for MySQL。 现在，客户可以选择为其数据库服务器设置最小 TLS 版本。 例如，如果将此最低 TLS 版本设置为1.0，则表示你应允许使用 TLS 1.0、1.1 和1.2 进行连接的客户端。 或者，将此选项设置为1.2 意味着仅允许使用 TLS 1.2 连接的客户端和 TLS 1.0 和 TLS 1.1 的所有传入连接将被拒绝。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置 TLS 配置

请按照以下步骤设置 MySQL server 最低 TLS 版本：

1. 在[Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 "MySQL 服务器" 页上的 "**设置**" 下，单击 "**连接安全性**" 以打开 "连接安全配置" 页。

1. 在 "**最低 TLS 版本**" 中，选择 " **1.2** " 以拒绝 MySQL 服务器的 tls 版本低于 tls 1.2 的连接。

    ![Azure Database for MySQL TLS 配置](./media/howto-tls-configurations/setting-tls-value.png)

1. 单击 **“确定”** ，保存这些更改。

1. 通知会确认已成功启用连接安全设置。

    ![Azure Database for MySQL TLS 配置成功](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-on-metric.md)。
