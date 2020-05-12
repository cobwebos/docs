---
title: Azure 蓝图概述
description: 了解如何通过 Azure 蓝图服务在 Azure 环境中创建、定义和部署项目。
ms.date: 05/06/2020
ms.topic: overview
ms.openlocfilehash: 68baeb8030caa17a9880cb0846688f1db6a15c87
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864498"
---
# <a name="what-is-azure-blueprints"></a>什么是 Azure 蓝图？

正如工程师或建筑师使用蓝图勾勒出项目的设计参数一样，通过 Azure 蓝图，云架构师和中心信息技术组同样可以定义一组可重复的 Azure 资源，这些资源实现并遵守组织的标准、模式和要求。 通过 Azure 蓝图，开发团队可以快速生成和构建新环境，并确信这些生成的环境符合组织规定，还可以使用一组有助于加快开发和交付过程的内置组件（如网络）。

蓝图是一种声明性方法，用于协调各个资源模板和其他项目的部署，例如：

- 角色分配
- 策略分配
- Azure 资源管理器模板
- 资源组

Azure 蓝图服务由全球分布的 [Azure Cosmos DB](../../cosmos-db/introduction.md) 提供支持。 蓝图对象将复制到多个 Azure 区域。 无论 Azure 蓝图将资源部署到哪个区域，此复制提供的对蓝图对象的访问都具有低延迟、高可用性和一致性。

## <a name="how-its-different-from-resource-manager-templates"></a>与资源管理器模板的不同之处

此服务旨在帮助进行  环境设置。 此设置通常包括一组资源组、策略、角色分配和资源管理器模板部署。 蓝图是将每个项目类型组合在一起的包，通过蓝图可编写和版本化该包（包括通过 CI/CD 管道）  。 最终可在一个可审计和跟踪的操作中将每个蓝图分配给订阅。

几乎所有要包含在 Azure 蓝图中部署的一切都可以使用资源管理器模板完成。 但是，资源管理器模板是 Azure 中不以本机方式存在的文档 – 每个模板均存储在本地或源代码管理中。 该模板用于部署一个或多个 Azure 资源，但部署了这些资源后，资源与模板之间就不再存在有效的连接或关系。

使用 Azure 蓝图，蓝图定义（应该部署的对象）和蓝图分配（已部署的对象）之间的关系仍然存在   。 此连接支持改进部署的跟踪和审核。 Azure 蓝图也能一次性升级由同一蓝图管理的多个订阅。

无需在资源管理器模板和蓝图之间进行选择。 每个蓝图可以包含零个或多个资源管理器模板项目  。 此支持意味着可以在 Azure 蓝图中重复使用以前开发和维护资源管理器模板库的工作成果。

## <a name="how-its-different-from-azure-policy"></a>与 Azure Policy 的不同之处

蓝图是一个包或容器，用于组合与 Azure 云服务、安全性和设计的实现相关的一组针对目标的标准、模式和要求，这些标准、模式和要求可以重复使用以确保一致性和符合性。

[策略](../policy/overview.md)是默认允许和显式拒绝系统，侧重于部署期间的资源属性，用于现有资源。 它会验证订阅中的资源是否符合要求和标准，以此为云治理提供支持。

在蓝图中包含策略可以在分配蓝图期间创建正确的模式或设计。 包含策略可确保只对环境进行批准或预期的更改，以确保持续符合蓝图意向。

策略可作为众多项目中的一项包含在蓝图定义中  。 蓝图还支持在策略和计划中使用参数。

## <a name="blueprint-definition"></a>蓝图定义

蓝图由项目组成  。 Azure 蓝图目前支持以下资源作为项目：

|资源  | 层次结构选项| 说明  |
|---------|---------|---------|
|资源组 | 订阅 | 创建新资源组以供蓝图中的其他项目使用。  通过这些占位符资源组，可以按照所需方式组织资源，并为包含的策略和角色分配项目以及 Azure 资源管理器模板提供范围限制。 |
|Azure 资源管理器模板 | 订阅、资源组 | 模板（包括嵌套模板和链接模板）用于构成复杂的环境。 示例环境：SharePoint 场、Azure 自动化状态配置或 Log Analytics 工作区。 |
|策略分配 | 订阅、资源组 | 将蓝图分配到订阅后，允许将策略或计划分配给该订阅。 该策略或计划必须位于蓝图定义位置的范围内。 若策略或计划具有参数，则在创建蓝图时或在蓝图分配期间分配这些参数。 |
|角色分配 | 订阅、资源组 | 将现有用户或组添加到内置角色，以确保始终为正确的人员提供正确的资源访问权限。 可为整个订阅定义角色分配，也可将其嵌套到蓝图所包含的特定资源组。 |

### <a name="blueprint-definition-locations"></a>蓝图定义位置

创建蓝图定义时，将定义蓝图的保存位置。 蓝图可以保存到你有**参与者**访问权限的[管理组](../management-groups/overview.md)或订阅。 如果位置是一个管理组，则蓝图可以分配给该管理组的任何子级订阅。

### <a name="blueprint-parameters"></a>蓝图参数

蓝图可以将参数传递给策略/计划或 Azure 资源管理器模板。
将任意项目添加到蓝图时，由创建者决定为每个蓝图分配提供定义的值，或者让每个蓝图分配在分配时提供一个值  。
这种灵活性让创建者可以为蓝图的所有使用定义预定值或者在分配时做出该决定。

> [!NOTE]
> 蓝图可以有自己的参数，但目前只能为从 REST API 而不是从门户生成的蓝图创建这些参数。

有关更多信息，请参阅[蓝图参数](./concepts/parameters.md)。

### <a name="blueprint-publishing"></a>蓝图发布

首次创建蓝图时，将视其为处于“草稿”模式  。 准备分配蓝图时，它必须处于“已发布”模式  。 发布需要定义“版本”字符串（字母、数字和连字符，最大长度为 20 个字符）以及可选的“更改注释”   。 该版本将其与针对同一蓝图的未来更改进行区别，并允许分配每个版本  。 此版本控制也意味着可将同一蓝图的不同版本分配给同一订阅  。 对蓝图进行其他更改时，除未发布的更改外，已发布
版本仍然存在    。 更改完成后，更新的蓝图是使用新的唯一版本发布的，现也可进行分配   。

## <a name="blueprint-assignment"></a>蓝图分配

可将蓝图的每个已发布版本（最大名称长度为 90 个字符）分配给现有订阅   。 在门户中，该蓝图默认使用最新发布的版本   。 若存在项目参数（或蓝图参数），则在分配过程中定义参数。

## <a name="permissions-in-azure-blueprints"></a>Azure 蓝图中的权限

若要使用蓝图，必须通过[基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC) 获得授权。 要创建蓝图，帐户需要以下权限：

- `Microsoft.Blueprint/blueprints/write` - 创建蓝图定义
- `Microsoft.Blueprint/blueprints/artifacts/write` - 在蓝图定义上创建项目
- `Microsoft.Blueprint/blueprints/versions/write` - 发布蓝图

要删除蓝图，帐户需要以下权限：

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> 必须在保存蓝图定义的管理组或订阅范围上授予或继承蓝图定义权限。

要分配或取消分配蓝图，帐户需要以下权限：

- `Microsoft.Blueprint/blueprintAssignments/write` - 分配蓝图
- `Microsoft.Blueprint/blueprintAssignments/delete` - 取消分配蓝图

> [!NOTE]
> 由于在订阅上创建了蓝图分配，因此必须在订阅范围授予蓝图分配和取消分配权限，或者将其继承到订阅范围。

以下内置角色可用：

|RBAC 角色 | 说明 |
|-|-|
|[所有者](../../role-based-access-control/built-in-roles.md#owner) | 除了其他权限以外，还包括所有与 Azure 蓝图相关的权限。 |
|[参与者](../../role-based-access-control/built-in-roles.md#contributor) | 除了其他权限以外，还可以创建和删除蓝图定义，但不具有蓝图分配权限。 |
|[蓝图参与者](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | 可以管理蓝图定义，但不能对其进行分配。 |
|[蓝图操作员](../../role-based-access-control/built-in-roles.md#blueprint-operator) | 可以指定现有已发布的蓝图，但不能创建新的蓝图定义。 仅当使用用户分配的托管标识完成分配时，蓝图分配才有效。 |

若这些内置角色不适合安全需求，请考虑创建[自定义角色](../../role-based-access-control/custom-roles.md)。

> [!NOTE]
> 如果使用系统分配的托管标识，则 Azure 蓝图的服务主体需要在分配的订阅上具有**所有者**角色才能启用部署。 若使用门户，则会自动为部署授予和撤消此角色。 若使用 REST API，则必须手动授予此角色，但在部署完成后仍会自动撤消此角色。 如果使用用户分配的托管标识，则仅创建蓝图分配的用户需要 `Microsoft.Blueprint/blueprintAssignments/write` 的权限，该权限包括在“所有者”和“蓝图运算符”内置角色中   。

## <a name="naming-limits"></a>命名限制

某些字段存在以下限制：

|对象|字段|允许的字符|最大 长度|
|-|-|-|-|
|蓝图|名称|字母、数字、连字符和句点|48|
|蓝图|版本|字母、数字、连字符和句点|20|
|蓝图分配|名称|字母、数字、连字符和句点|90|
|蓝图项目|名称|字母、数字、连字符和句点|48|

## <a name="video-overview"></a>视频概述

以下 Azure 蓝图概述来自 Azure Fridays。 如需下载视频，请访问第 9 频道的 [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints)（Azure Fridays - Azure 蓝图概述）。

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>后续步骤

- [创建蓝图 - 门户](./create-blueprint-portal.md)。
- [创建蓝图 - PowerShell](./create-blueprint-powershell.md)。
- [创建蓝图 - REST API](./create-blueprint-rest-api.md)。