---
title: Azure CLI 脚本示例 - 发布资产 | Microsoft Docs
description: 本文演示如何使用 Azure CLI 脚本发布资产。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3b3c358a84dd74595c476f029a1c8f28bc3c901f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295863"
---
# <a name="cli-example-publish-an-asset"></a>CLI 示例：发布资产

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文中的 Azure CLI 脚本演示如何创建流式处理定位符并返回流式处理 URL。 

## <a name="prerequisites"></a>必备条件 

[创建媒体服务帐户](./create-account-howto.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>后续步骤

[媒体服务概述](media-services-overview.md)
