---
title: Azure CLI 脚本示例 - 创建 Azure 事件网格订阅 | Microsoft Docs
description: 本主题中的 Azure CLI 脚本演示如何为作业状态更改创建一个帐户级别的事件网格订阅。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098899"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI 示例：创建 Azure 事件网格订阅 

本文中的 Azure CLI 脚本演示如何为作业状态更改创建一个帐户级别的事件网格订阅。

## <a name="prerequisites"></a>先决条件 

[创建媒体服务帐户](../create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>后续步骤

有关详细示例，请参阅 [Azure CLI 示例](../cli-samples.md)。
