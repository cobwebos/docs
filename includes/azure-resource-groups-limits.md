---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334947"
---
| 资源 | 限制 |
| --- | --- |
| 每个[资源组的资源](../articles/azure-resource-manager/management/overview.md#resource-groups) | 资源不受资源组的限制。 相反，它们受资源组中的资源类型的限制。 请参阅下一行。 |
| 每个资源组的资源，每个资源类型 |800 - 某些资源类型可能超过 800 限制。 请参阅[不限于每个资源组 800 个实例的资源](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)。 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |
| 每个部署的资源数 |800 |
| 管理锁数（按唯一的作用域） |20 |
| 标记数（按资源或资源组） |50 |
| 标记键长度 |512 |
| 标记值长度 |256 |

<sup>1</sup>如果达到每个资源组的部署数限制 800，则会从历史记录中删除不再需要的部署。 从部署历史记录中删除条目不会影响已部署的资源。 有关详细信息，请参阅[解决部署计数超出 800 的错误](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)。

#### <a name="template-limits"></a>模板限制

| “值” | 限制 |
| --- | --- |
| 参数 |256 |
| 变量 |256 |
| 资源（包括副本计数） |800 |
| Outputs |64 |
| 模板表达式 |24,576 个字符 |
| 已导出模板中的资源 |200 |
| 模板大小 |4 MB |
| 参数文件大小 |64 KB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/templates/linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
