---
title: 将 Service Fabric 网格应用程序移动到另一个区域
description: 可以通过将当前模板的副本部署到新的 Azure 区域来移动 Service Fabric 网格资源。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: c842a065f108a924c6bffd70d6c2edbbd31b6dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260145"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>将 Service Fabric 网格应用程序移动到另一个 Azure 区域

本文介绍如何将 Service Fabric 网格应用程序及其资源移到不同的 Azure 区域。 由于各种原因，你可能需要将资源移动到其他区域。 例如，为了应对中断，只需获取特定区域中提供的功能或服务，以满足内部策略和监管要求，或者响应容量规划要求。

 [Service Fabric 网格不支持](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) 在 Azure 区域之间直接移动资源。 但是，你可以通过将当前 Azure 资源管理器模板的副本部署到新的目标区域，然后将流量和依赖项重定向到新创建的 Service Fabric 网格应用程序，间接移动资源。

## <a name="prerequisites"></a>必备条件

* 入口控制器 (例如， [应用程序网关](../application-gateway/index.yml)) 用于在客户端与 Service Fabric 网格应用程序之间路由流量的媒介
* Service Fabric 网格 (预览版在目标 Azure 区域中) 可用性 (`westus` 、 `eastus` 或 `westeurope`) 

## <a name="prepare"></a>准备

1. 通过从最近的部署导出 Azure 资源管理器模板和参数，拍摄 Service Fabric 网格应用程序当前状态的 "快照"。 为此，请按照使用 Azure 门户 [部署后的导出模板](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) 中的步骤操作。 也可以使用 [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) 或 [REST API](/rest/api/resources/resourcegroups/exporttemplate)。

2. 如果适用，请 [导出同一资源组中的其他资源](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) ，以便在目标区域中重新部署。

3. 查看 (并编辑（如果需要）) 导出的模板，以确保现有属性值是要在目标区域中使用的值。 新的 `location` Azure 区域 () 是在重新部署过程中将提供的参数。

## <a name="move"></a>移动

1. 在目标区域中创建新的资源组 (或使用现有的资源组) 。

2. 使用导出的模板，按照使用 Azure 门户 [从自定义模板部署资源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) 中的步骤操作。 也可以使用 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 或 [REST API](../azure-resource-manager/templates/deploy-rest.md)。

3. 有关移动相关资源（例如 [Azure 存储帐户](../storage/common/storage-account-move.md)）的指南，请参阅 [跨区域移动 Azure 资源](../azure-resource-manager/management/move-region.md)主题中列出的各个服务的指南。

## <a name="verify"></a>验证

1. 部署完成后，请测试 (的应用程序终结点) ，验证应用程序的功能。

2. 你还可以通过检查应用程序状态 ([az 网格应用显示](/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) 并查看应用程序日志，并 ([az 网格代码包-记录](/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) [Service Fabric](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli)命令来验证应用程序的状态。

## <a name="commit"></a>Commit

确认目标区域中 Service Fabric 网格应用程序的等效功能后，配置入口控制器 (例如， [应用程序网关](../application-gateway/redirect-overview.md)) ，将流量重定向到新应用程序。

## <a name="clean-up-source-resources"></a>清理源资源

若要完成移动 Service Fabric 网格应用程序，请 [删除源应用程序和/或父资源组](../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>后续步骤

* [跨区域移动 Azure 资源](../azure-resource-manager/management/move-region.md)
* [跨区域移动 Azure 资源的支持](../azure-resource-manager/management/region-move-support.md)
* [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [资源移动操作支持](../azure-resource-manager/management/move-support-resources.md
)
