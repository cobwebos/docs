---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3dc09de6afaddeb06b0243eb46e888b673109545
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505729"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个资源[资源组](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)，每种资源类型 |800 |因资源类型而有所不同 |
| 部署历史记录中每个资源组的部署数 |800 |800 |
| 每个部署的资源数 |800 |800 |
| 每个唯一的作用域管理锁 |20 |20 |
| 每个资源或资源组的标记数 |15 |15 |
| 标记键长度 |512 |512 |
| 标记值长度 |256 |256 |


#### <a name="template-limits"></a>模板限制

| 值 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 参数 |256 |256 |
| 变量 |256 |256 |
| 资源，包括副本计数 |800 |800 |
| 输出 |64 |64 |
| 模板表达式 |24,576 个字符 |24,576 个字符 |
| 已导出模板中的资源 |200 |200 | 
| 模板大小 |1 MB |1 MB |
| 参数文件大小 |64 KB |64 KB |

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。

如果达到每个资源组的部署数限制 800，则会从历史记录中删除不再需要的部署。 您可以从使用历史记录中删除条目[az 组部署删除](/cli/azure/group/deployment)适用于 Azure CLI。 您还可以使用[删除 AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)在 PowerShell 中。 从部署历史记录中删除条目不会影响部署资源。 