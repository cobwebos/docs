---
title: TLS 配置-Azure 门户-Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 设置 TLS 配置
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 46eaa6a3b97967da9c4743d0cf1f6edc8f90b1ce
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119778"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 中配置 TLS 设置

本文介绍如何将 Azure Database for MySQL 服务器配置为强制执行连接所允许的最低 TLS 版本，并拒绝与已配置的最低 TLS 版本相比，使网络安全性更低的所有连接。

可以强制 TLS 版本用于连接到其 Azure Database for MySQL。 现在，客户可以选择为其数据库服务器设置最低 TLS 版本。 例如，如果将此最低 TLS 版本设置为1.0，则表示你应允许使用 TLS 1.0、1.1 和1.2 进行连接的客户端。 或者，将此选项设置为1.2 意味着仅允许使用 TLS 1.2 + 的客户端和 TLS 1.0 和 TLS 1.1 的所有传入连接被拒绝。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置 TLS 配置

请按照以下步骤设置 MySQL server 最低 TLS 版本：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

1. 在 "MySQL 服务器" 页上的 "**设置**" 下，单击 "**连接安全性**" 以打开 "连接安全配置" 页。

1. 在 "**最低 TLS 版本**" 中，选择 " **1.2** " 以拒绝 MySQL 服务器的 tls 版本低于 tls 1.2 的连接。

    ![Azure Database for MySQL TLS 配置](./media/howto-tls-configurations/setting-tls-value.png)

1. 单击“保存”以保存更改。

1. 通知会确认已成功启用连接安全设置。

    ![Azure Database for MySQL TLS 配置成功](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何创建有关指标的警报](howto-alert-on-metric.md)