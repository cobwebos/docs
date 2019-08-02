---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/30/2019
ms.author: tomfitz
ms.openlocfilehash: f027f5b295d75c105e81360a10718239e5b08e4d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698274"
---
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[资源组](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)的资源数（按资源类型） |800 |某些资源类型可能超过800限制。 请参阅[每个资源组不限于800实例的资源](../articles/azure-resource-manager/resources-without-rg-limit.md)。 |
| 部署历史记录中每个资源组的部署数 |800<sup>1</sup> |800 |
| 每个部署的资源数 |800 |800 |
| 管理锁数（按唯一的作用域） |20 |20 |
| 标记数（按资源或资源组） |15 |15 |
| 标记键长度 |512 |512 |
| 标记值长度 |256 |256 |

<sup>1</sup>如果达到每个资源组的部署数限制 800，则会从历史记录中删除不再需要的部署。 从部署历史记录中删除条目不会影响已部署的资源。 可以使用 Azure CLI 的 [az group deployment delete](/cli/azure/group/deployment) 或 PowerShell 中的 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 删除历史记录中的条目。  有关在持续集成和持续交付 (CI/CD) 方案中用于自动删除部署的 PowerShell 脚本，请参阅 [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)。

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
