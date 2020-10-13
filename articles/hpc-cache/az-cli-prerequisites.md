---
title: Azure HPC 缓存 Azure CLI 先决条件
description: 设置步骤，然后才能使用 Azure CLI 来创建或修改 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096550"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>设置适用于 Azure HPC Cache 的 Azure CLI

使用 Azure CLI 创建或管理 Azure HPC 缓存之前，请按照以下步骤准备环境。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>安装 Azure CLI

Azure HPC 缓存要求 Azure CLI 版本2.7 或更高版本。 运行 `az --version` 查看安装的版本和依赖库。 若要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>安装 Azure HPC 缓存扩展

Azure HPC 缓存函数不是主要基本代码的一部分，因此还必须安装扩展引用。 请根据以下说明操作。

1. 登录

   如果使用的是本地安装的 CLI 版本，请使用 [az login](/cli/azure/reference-index#az-login) 命令登录。

    ```azurecli
    az login
    ```

    遵循终端中显示的步骤完成身份验证过程。

   > [!TIP]
   > 如果有多个订阅，则需要选择一个。 当你在 Azure 门户中启动 Cloud Shell 会话时，请选择它，或者按照 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli#sign-in) 中的说明从命令行设置你的订阅。

2. 安装 Azure CLI 扩展

   Azure HPC 缓存函数作为 Azure CLI 扩展提供-它尚不属于核心 CLI 包。 你需要先安装扩展引用，然后才能使用它。

   Azure CLI 扩展使你能够访问试验性和预发布命令。 若要了解有关扩展的详细信息，包括更新和卸载，请参阅 [将扩展用于 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

   通过运行以下命令安装 Azure HPC 缓存的扩展：

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>设置默认资源组 (可选) 

大多数 hpc 缓存命令要求您传递缓存的资源组。 你可以使用 [az configure](/cli/azure/reference-index#az-configure)设置默认资源组。

## <a name="next-steps"></a>后续步骤

安装 Azure CLI 扩展并登录后，可以使用 Azure CLI 来创建和管理 Azure HPC 缓存系统。

* [创建 Azure HPC 缓存](hpc-cache-create.md)
* [Azure CLI hpc-缓存文档](/cli/azure/ext/hpc-cache/hpc-cache)
