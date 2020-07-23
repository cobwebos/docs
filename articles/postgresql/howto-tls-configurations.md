---
title: TLS 配置-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 了解如何为 Azure Database for PostgreSQL 单一服务器使用 Azure 门户设置 TLS 配置
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 4cf491a27fbe53a5f5bf0e8351e5bb684b3492f1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101981"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>使用 Azure 门户在 Azure Database for PostgreSQL 单服务器中配置 TLS 设置

本文介绍如何配置 Azure Database for PostgreSQL 来强制执行连接所允许的最低 TLS 版本，并拒绝与已配置的最低 TLS 版本相比，使网络安全性更低的所有连接。

可以强制 TLS 版本用于连接到其 Azure Database for PostgreSQL。 现在，客户可以选择为其数据库服务器设置最低 TLS 版本。 例如，如果将最小 TLS 设置版本设置为 TLS 1.0，则意味着服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端连接。 相反，将最低 tls 版本设置为 1.2 + 意味着仅允许使用 TLS 1.2 的客户端进行连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有连接。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>为 Azure Database for PostgreSQL-单一服务器设置 TLS 配置

请按照以下步骤设置 PostgreSQL 最小 TLS 版本：

1. 在[Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for PostgreSQL。

1.  在 "Azure Database for PostgreSQL-单一服务器" 页上的 "**设置**" 下，单击 "**连接安全性**" 以打开 "连接安全配置" 页。

1. 在 "**最低 TLS 版本**" 中，选择 " **1.2** " 以拒绝 POSTGRESQL 单一服务器的 tls 版本低于 tls 1.2 的连接。

    ![Azure Database for PostgreSQL 单服务器 TLS 配置](./media/howto-tls-configurations/setting-tls-value.png)

1. 单击“保存”以保存更改。

1. 通知会确认已成功启用连接安全设置。

    ![Azure Database for PostgreSQL-单服务器 TLS 配置成功](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-on-metric.md)