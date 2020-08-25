---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: f0ab7c2efc499c43245680e56a7e5ca1b5261397
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748769"
---
| 资源 | 限制 |
| --- | --- |
| 每个[资源组](../articles/azure-resource-manager/management/overview.md#resource-groups)的资源 | 资源不受资源组限制。 相反，它们受资源组中资源类型的限制。 请参阅下一行。 |
| 每个资源组的资源（按资源类型） |800 - 某些资源类型可能超过 800 的限制。 请参阅[不限于每个资源组 800 个实例的资源](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)。 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |
| 每个部署的资源数 |800 |
| 管理锁数（按唯一的[作用域](../articles/azure-resource-manager/management/overview.md#understand-scope)）  |20 个 |
| 标记数（按资源或资源组） |50 |
| 标记键长度 |512 |
| 标记值长度 |256 |

<sup>1</sup>从 2020 年 6 月开始，在你接近限制时，部署将自动从历史记录中删除。 从部署历史记录中删除条目不会影响已部署的资源。 有关详细信息，请参阅[从部署历史记录中自动删除](../articles/azure-resource-manager/templates/deployment-history-deletions.md)。

#### <a name="template-limits"></a>模板限制

| Value | 限制 |
| --- | --- |
| parameters |256 |
| 变量 |256 |
| 资源（包括副本计数） |800 |
| Outputs |64 |
| 模板表达式 |24,576 个字符 |
| 已导出模板中的资源 |200 |
| 模板大小 |4 MB |
| 参数文件大小 |64 KB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/templates/linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
