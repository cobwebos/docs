---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b4338560d515289d3e9aedfa716ec7c3ace51131
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392255"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个资源[组](../articles/azure-resource-manager/management/overview.md#resource-groups)的资源数（按资源类型） |800 |某些资源类型可能超过800限制。 请参阅[每个资源组不限于800实例的资源](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)。 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |800 |
| 每个部署的资源数 |800 |800 |
| 每个唯一作用域的管理锁 |20 |20 |
| 每个资源或资源组的标记数 |50 |50 |
| 标记键长度 |512 |512 |
| 标记值长度 |256 |256 |

<sup>1</sup>如果每个资源组的部署限制为800，请从不再需要的历史记录中删除部署。 从部署历史记录中删除条目不会影响部署的资源。 有关详细信息，请参阅[在部署计数超过800时解决错误](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)。

#### <a name="template-limits"></a>模板限制

| 值 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 参数 |256 |256 |
| 变量 |256 |256 |
| 资源（包括副本计数） |800 |800 |
| Outputs |64 |64 |
| 模板表达式 |24,576 个字符 |24,576 个字符 |
| 已导出模板中的资源 |200 |200 | 
| 模板大小 |4 MB |4 MB |
| 参数文件大小 |64 KB |64 KB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/templates/linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
