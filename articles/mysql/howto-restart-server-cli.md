---
title: 重新启动 MySQL 服务器使用 Azure CLI 的 Azure 数据库
description: 本文介绍如何重新启动 Azure Database for MySQL 服务器使用 Azure CLI。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: d00aa35437f93c010ce48f3036b4a684910702c0
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623148"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>重新启动 MySQL 服务器使用 Azure CLI 的 Azure 数据库
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

重新启动服务器使用以下命令：

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

了解有关[如何在 Azure 数据库中为 MySQL 设置参数](howto-configure-server-parameters-using-cli.md)