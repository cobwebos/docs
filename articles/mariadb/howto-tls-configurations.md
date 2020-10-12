---
title: TLS 配置 - Azure 门户 - Azure Database for MariaDB
description: 了解如何使用 Azure 门户为 Azure Database for MariaDB 设置 TLS 配置
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: fac719daf05e8b319db7c86d0dbc61c2814b0a0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120341"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>使用 Azure 门户在 Azure Database for MariaDB 中配置 TLS 设置

本文介绍如何配置 Azure Database for MariaDB 服务器，以强制设置连接所允许的最低 TLS 版本并拒绝所有使用更低 TLS 版本（与已配置的最低 TLS 版本相比）的连接，从而增强网络安全性。

可以强制设置连接到其 Azure Database for MariaDB 所需的 TLS 版本，方法是为其数据库服务器设置最低 TLS 版本。 例如，将最低 TLS 设置版本设置为 TLS 1.0 意味着服务器将允许使用 TLS 1.0、1.1 和 1.2+ 的客户端进行连接。 也可将此选项设置为 1.2，这意味着仅允许那些使用 TLS 1.2+ 的客户端进行连接，将拒绝使用 TLS 1.0 和 TLS 1.1 进行的所有连接。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

* [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>为 Azure Database for MariaDB 设置 TLS 配置

请按照以下步骤设置 MariaDB 服务器的最低 TLS 版本：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MariaDB 服务器。

1. 在 MariaDB 服务器页上的“设置”下，单击“连接安全性”，打开连接安全性配置页 。

1. 在“最低 TLS 版本”中，选择“1.2”，为 MariaDB 服务器拒绝 TLS 版本低于 TLS 1.2 的连接 。

    ![Azure Database for MariaDB 的 TLS 配置](./media/howto-tls-configurations/tls-configurations.png)

1. 单击“保存”  以保存更改。

1. 此时会显示一则通知，确认连接安全性设置已成功启用。

    ![Azure Database for MariaDB 的 TLS 配置成功](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-metric.md)