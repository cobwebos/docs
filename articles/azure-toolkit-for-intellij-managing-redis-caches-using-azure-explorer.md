---
title: "使用用于 IntelliJ 的 Azure 资源管理器管理 Redis 缓存 | Microsoft Docs"
description: "了解如何使用用于 IntelliJ 的 Azure 资源管理器管理 Azure redis 缓存。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/14/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 9ab8ae17ee2a92b5b16d2210366c00b5b8023fa8
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---

# <a name="managing-redis-caches-using-the-azure-explorer-for-intellij"></a>使用用于 IntelliJ 的 Azure 资源管理器管理 Redis 缓存

Azure 资源管理器是用于 IntelliJ 的 Azure 工具包的一部分，它为 Java 开发人员提供易于使用的解决方案，用于从 IntelliJ IDE 内部管理其 Azure 帐户中的 redis 缓存。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-intellij"></a>使用 IntelliJ 创建 Redis 缓存

以下步骤将引导完成使用 Azure 资源管理器创建 redis 缓存的步骤。

1. 按照[用于 IntelliJ 的 Azure 工具包的登录说明]一文中的步骤登录到 Azure 帐户。

1. 在“Azure 资源管理器”工具窗口中，展开“Azure”节点，右键单击“Redis 缓存”，然后单击“创建 Redis 缓存”。

   ![创建 Redis 缓存菜单][CR01]

1. 当“新建 Redis 缓存”对话框出现时，请指定以下选项：

   ![“新建 Redis 缓存”对话框][CR02]

   a. DNS 名称：为新的 redis 缓存指定 DNS 子域，预设为“redis.cache.windows.net”，例如：wingtiptoys.redis.cache.windows.net。

   b. 订阅：指定要用于新的 redis 缓存的 Azure 订阅。

   c. 资源组：为 redis 缓存指定资源组；需要选择以下一种选项：
      * **新建**：指定要创建新的资源组。
      * 使用现有资源组：指定将从与 Azure 帐户关联的资源组列表中选择。

   d.单击“下一步”。 位置：指定创建 redis 缓存的位置，例如：美国西部。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 定价层： 指定 redis 缓存使用的定价层；此设置将确定客户端连接数。 （有关详细信息，请参阅 [Redis 缓存定价]。）

   f. 非 SSL 端口：指定 redis 缓存是否允许非 SSL 连接；默认情况下，仅允许 SSL 连接。

1. 指定 redis 缓存的所有设置后，单击“确定”。

redis 缓存创建完成后，将显示在 Azure 资源管理器中。

   ![Azure 资源管理器中的 Redis 缓存][CR03]

> [!NOTE]
>
> 有关配置 Azure redis 缓存设置的详细信息，请参阅[如何配置 Azure Redis 缓存]。
>

## <a name="display-the-properties-for-your-redis-cache-in-intellij"></a>在 IntelliJ 中显示 Redis 缓存属性

1. 在 Azure 资源管理器中，右键单击 redis 缓存，然后单击“显示属性”。

   ![使用 Azure 资源管理器上下文菜单显示 redis 缓存属性][SP01]

1. Azure 资源管理器显示 redis 缓存属性。

   ![Redis 缓存属性][SP02]

## <a name="delete-your-redis-cache-by-using-intellij"></a>使用 IntelliJ 删除 Redis 缓存

1. 在 Azure 资源管理器中，右键单击 redis 缓存，然后单击“删除”。

   ![使用 Azure 资源管理器上下文菜单删除 redis 缓存][DE01]

1. 系统提示删除 redis 缓存时，单击“是”。

   ![删除 redis 缓存提示][DE02]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

有关 Azure redis 缓存、配置设置和定价的详细信息，请参阅以下链接：

* [Azure Redis 缓存]
* [Redis 缓存文档]
* [Redis 缓存定价]
* [如何配置 Azure Redis 缓存]

<!-- URL List -->

[Redis 缓存定价]: https://azure.microsoft.com/pricing/details/cache/
[Azure Redis 缓存]: https://azure.microsoft.com/services/cache/
[Redis 缓存文档]: ./redis-cache/index.md
[如何配置 Azure Redis 缓存]: ./redis-cache/cache-configure.md
[用于 IntelliJ 的 Azure 工具包的登录说明]: ./azure-toolkit-for-intellij-sign-in-instructions.md

<!-- IMG List -->

[CR01]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-intellij-managing-redis-caches-using-azure-explorer/DE02.png

