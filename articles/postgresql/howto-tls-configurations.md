---
title: TLS 配置-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 了解如何为 Azure Database for PostgreSQL 单一服务器使用 Azure 门户设置 TLS 配置
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375104"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>使用 Azure 门户在 Azure Database for PostgreSQL 单服务器中配置 TLS 设置

本文介绍如何将 Azure Database for PostgreSQL 单服务器配置为对所有传入连接强制实施最低 TLS 版本，这有助于提高网络安全性。

客户现在能够强制 TLS 版本连接到其 Azure Database for PostgreSQL 单服务器。 现在，客户可以选择为其数据库服务器设置最小 TLS 版本。 例如，如果将最小 TLS 设置版本设置为 TLS 1.0，则意味着服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端连接。 或者，将此选项设置为1.2 意味着仅允许使用 TLS 1.2 的客户端进行连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有连接。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>为 Azure Database for PostgreSQL-单一服务器设置 TLS 配置

请按照以下步骤设置 PostgreSQL 单服务器最低 TLS 版本：

1. 在[Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for PostgreSQL 单一服务器。

1.  在 "Azure Database for PostgreSQL-单一服务器" 页上的 "**设置**" 下，单击 "**连接安全性**" 以打开 "连接安全配置" 页。

1. 在 "**最低 TLS 版本**" 中，选择 " **1.2** " 以拒绝 POSTGRESQL 单一服务器的 tls 版本低于 tls 1.2 的连接。

    ![Azure Database for PostgreSQL 单服务器 TLS 配置](./media/howto-tls-configurations/setting-tls-value.png)

1. 单击 **“确定”** ，保存这些更改。

1. 通知会确认已成功启用连接安全设置。

    ![Azure Database for PostgreSQL-单服务器 TLS 配置成功](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-on-metric.md)。
