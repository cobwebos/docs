---
title: 获取事件网格资源的访问键
description: 本文介绍如何获取事件网格主题或域的访问密钥
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: e5694fe0b5f22f7f76285c344627005ea727ae3c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105857"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>获取事件网格资源（主题或域）的访问键
访问密钥用于对 Azure 事件网格资源（主题和域）的应用程序发布事件进行身份验证。 建议定期重新生成密钥并将其安全地存储。 提供两个访问密钥，以便您可以在重新生成其他密钥时使用一个密钥维护连接。

本文介绍如何使用 Azure 门户、PowerShell 或 CLI 获取事件网格资源（主题或域）的访问密钥。 

## <a name="azure-portal"></a>Azure 门户
在 Azure 门户中，切换到主题或域的**事件网格主题**或**事件网格域**页的 "**访问密钥**" 选项卡。  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="访问密钥页":::

## <a name="azure-powershell"></a>Azure PowerShell
使用[AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0)命令获取主题的访问密钥。 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

使用[AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0)命令获取域的访问密钥。 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
使用[az eventgrid 主题 key list](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list)获取主题的访问密钥。 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

使用[az eventgrid domain key list](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list)获取域的访问密钥。 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>后续步骤
请参阅以下文章：[对发布客户端进行身份验证](security-authenticate-publishing-clients.md)。 
