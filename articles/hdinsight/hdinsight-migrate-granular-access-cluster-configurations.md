---
title: 更改群集配置访问-Azure HDInsight
description: 了解有关访问敏感的群集配置字段的更改。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610188"
---
# <a name="changes-to-cluster-configuration-access"></a>对群集配置访问权限的更改

Azure HDInsight SDK 的最新版本带来了一些重要的更改，以支持多个细粒度的基于角色的访问以获取敏感信息。 当属于这些更改时，某些**可能需要操作**如果将受影响的方法之一。

## <a name="sdk-for-net-500"></a>适用于.NET 5.0.0 SDK

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 将**不再返回敏感参数**，例如存储密钥 （core 站点） 或 HTTP 凭据 （网关）。
    - 若要检索所有配置，包括敏感参数使用`ConfigurationOperationsExtensions.List`今后。  请注意，具有读取者角色的用户将不能使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 若要检索仅 HTTP 网关凭据，请使用`ClusterOperationsExtensions.GetGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 现已弃用，已由`ClusterOperationsExtensions.UpdateGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 并[ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)现已弃用。 现在始终启用 HTTP，因此不再需要这些方法。