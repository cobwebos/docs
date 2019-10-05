---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 1190798b234f9c73e02fda41c03ffa296246be63
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71975277"
---
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[资源组](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)的资源数（按资源类型） |800 |某些资源类型可能超过 800 的限制。 请参阅[不限于每个资源组 800 个实例的资源](../articles/azure-resource-manager/resources-without-rg-limit.md)。 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |800 |
| 每个部署的资源数 |800 |800 |
| 管理锁数（按唯一的作用域） |20 |20 |
| 标记数（按资源或资源组） |50 |50 |
| 标记键长度 |512 |512 |
| 标记值长度 |256 |256 |

<sup>1</sup>如果达到每个资源组的部署数限制 800，则会从历史记录中删除不再需要的部署。 从部署历史记录中删除条目不会影响已部署的资源。 有关详细信息，请参阅[在部署计数超过800时解决错误](../articles/azure-resource-manager/deployment-quota-exceeded.md)。

#### <a name="template-limits"></a>模板限制

| ReplTest1 | 默认限制 | 最大限制 |
| --- | --- | --- |
| Parameters |256 |256 |
| 变量 |256 |256 |
| 资源（包括副本计数） |800 |800 |
| outputs |64 |64 |
| 模板表达式 |24,576 个字符 |24,576 个字符 |
| 已导出模板中的资源 |200 |200 | 
| 模板大小 |4 MB |4 MB |
| 参数文件大小 |64 KB |64 KB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
