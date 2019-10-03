---
title: Azure 事件网格-故障排除指南
description: 本文提供了错误代码、错误消息、说明和建议操作的列表。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: c4c4e7980247e0ce41c685873a2d50b3e37fb405
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061471"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>排查 Azure 事件网格错误
此故障排除指南提供了 Azure 事件网格错误代码、错误消息、说明以及在收到这些错误时应采取的建议操作的列表。 

## <a name="error-code-400"></a>错误代码：400
| 错误代码 | 错误消息 | 描述 | 建议 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | 主题名称的长度必须介于3到50个字符之间。 | 自定义主题名称的长度应在3到50个字符之间。 主题名称中只允许使用字母数字字符、数字和 "-" 字符。 此外, 名称不应以以下保留字开头: <ul><li>Microsoft</li><li>EventGrid</li><li>系统</li></ul> | 请选择符合主题名称要求的其他主题名称。 |
| HttpStatusCode. BadRequest<br/>400 | 域名长度必须介于3到50个字符之间。 | 域名长度应介于3到50个字符之间。 主题名称中只允许使用字母数字字符、数字和 "-" 字符。 此外, 名称不应以以下保留字开头:<ul><li>Microsoft</li><li>EventGrid</li><li>系统</li> | 请选择符合域名要求的其他域名。 |
| HttpStatusCode. BadRequest<br/>400 | 过期时间无效。 | 事件订阅的过期时间决定了何时停用事件订阅。 此值应为将来的有效日期时间值。| 请确保事件订阅过期时间为有效的日期时间格式, 并将其设置为将来的时间。 |

## <a name="error-code-409"></a>错误代码：409
| 错误代码 | 错误消息 | 描述 | 推荐的操作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode 冲突 <br/>409 | 已存在具有指定名称的主题。 选择其他主题名称。   | 在单个 Azure 区域中, 自定义主题名称应是唯一的, 以便确保正确发布操作。 同一名称可在不同的 Azure 区域中使用。 | 为主题选择其他名称。 |
| HttpStatusCode 冲突 <br/> 409 | 具有指定的域已存在。 请选择其他域名。 | 域名在单个 Azure 区域中应该是唯一的, 以便确保正确发布操作。 同一名称可在不同的 Azure 区域中使用。 | 为域选择不同的名称。 |
| HttpStatusCode 冲突<br/>409 | 已达到配额限制。 有关这些限制的详细信息, 请参阅[Azure 事件网格限制](../azure-subscription-service-limits.md#event-grid-limits)。  | 每个 Azure 订阅都对可使用的 Azure 事件网格资源的数量有限制。 已超过此配额的部分或全部, 无法再创建更多的资源。 |  检查当前的资源使用情况, 并删除不需要的任何资源。 如果仍需要增加配额, 请将电子邮件发送到[aeg@microsoft.com](mailto:aeg@microsoft.com)所需的确切资源数。 |


## <a name="next-steps"></a>后续步骤
如果需要更多帮助, 请将你的问题发布到[Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid), 或提出[支持票证](https://azure.microsoft.com/support/options/)。 
