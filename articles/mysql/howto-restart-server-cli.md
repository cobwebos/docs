---
title: 重新启动服务器-Azure CLI-Azure Database for MySQL
description: 本文介绍如何使用 Azure CLI 重新启动 Azure Database for MySQL 服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 146c531805ff22544797c44c8df32b0e5d1e83b0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774186"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>使用 Azure CLI 重新启动 Azure Database for MySQL 服务器
本主题介绍如何重启 Azure Database for MySQL 服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可以处理缩放 vCores 等先前请求的操作。

完成重启所需的时间取决于 MySQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

## <a name="prerequisites"></a>必备组件
若要完成本操作指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作方法指南要求使用 Azure CLI 版本 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。


## <a name="restart-the-server"></a>重启服务器

用以下命令重新启动服务器：

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

了解[如何在 Azure Database for MySQL 中设置参数](howto-configure-server-parameters-using-cli.md)