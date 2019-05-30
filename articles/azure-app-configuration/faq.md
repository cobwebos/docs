---
title: Azure 应用程序配置常见问题 |Microsoft Docs
description: 有关 Azure 应用程序配置常见问题
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393350"
---
# <a name="azure-app-configuration-faq"></a>Azure 应用程序配置常见问题

本文介绍有关 Azure 应用程序配置常见问题。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>应用程序配置与 Azure Key Vault 有何不同？

应用配置设计的一组不同的用例： 它可帮助开发人员管理应用程序设置和控制功能的可用性。 其目标是简化许多使用复杂的配置数据的任务。

应用配置支持：

- 分层命名空间
- 标记
- 大量查询
- 批量检索
- 专用的管理操作
- 功能管理用户界面

应用配置密钥保管库的补充，应在大多数应用程序部署中同时使用这两个。

## <a name="should-i-store-secrets-in-app-configuration"></a>应在应用程序配置中存储机密？

虽然应用配置提供了稳固的安全，密钥保管库仍是用于存储应用程序机密的最佳位置。 密钥保管库提供硬件级别的加密、 精细的访问策略和管理操作，如证书旋转。

## <a name="does-app-configuration-encrypt-my-data"></a>应用配置是否加密数据？

是的。 应用配置加密所有键值保存，并对网络通信进行都加密。 键名用作索引来检索配置数据和未加密。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何应存储多个环境 （测试、 过渡、 生产等） 的配置？

当前可以控制谁在每个存储级别有权访问应用程序配置。 为每个需要不同的权限的环境中使用独立的存储库。 这种方法为您提供了最佳的安全隔离。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>将应用配置的建议的方式是什么？

请参阅[最佳做法](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>应用配置费用是多少？

该服务可以自由地在公共预览版中使用。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何报告问题或提供建议？

您可以通过直接在我们[GitHub](https://github.com/Azure/AppConfiguration/issues)。

## <a name="next-steps"></a>后续步骤

* [有关 Azure 应用程序配置](./overview.md)
