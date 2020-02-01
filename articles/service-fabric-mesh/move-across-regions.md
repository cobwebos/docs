---
title: 将 Service Fabric 网格应用程序移动到另一个区域
description: 可以通过将当前模板的副本部署到新的 Azure 区域来移动 Service Fabric 网格资源。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908158"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>将 Service Fabric 网格应用程序移动到另一个 Azure 区域

本文介绍如何将 Service Fabric 网格应用程序及其资源移到不同的 Azure 区域。 出于多种原因，你可以将资源移到另一个区域。 例如，为了应对中断，只需获取特定区域中提供的功能或服务，以满足内部策略和监管要求，或者响应容量规划要求。

 [Service Fabric 网格不支持](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh)在 Azure 区域之间直接移动资源。 但是，你可以通过将当前 Azure 资源管理器模板的副本部署到新的目标区域，然后将流量和依赖项重定向到新创建的 Service Fabric 网格应用程序，间接移动资源。

## <a name="prerequisites"></a>Prerequisites

* 入口控制器（如[应用程序网关](https://docs.microsoft.com/azure/application-gateway/)），用作在客户端与 Service Fabric 网格应用程序之间路由流量的媒介
* 目标 Azure 区域中的 Service Fabric 网格（预览）可用性（`westus`、`eastus`或 `westeurope`）

## <a name="prepare"></a>快照

1. 通过从最近的部署导出 Azure 资源管理器模板和参数，拍摄 Service Fabric 网格应用程序当前状态的 "快照"。 为此，请按照使用 Azure 门户[部署后的导出模板](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)中的步骤操作。 你还可以使用[Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、 [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)。

2. 如果适用，请[导出同一资源组中的其他资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group)，以便在目标区域中重新部署。

3. 查看（并根据需要编辑）导出的模板，以确保现有属性值是要在目标区域中使用的属性值。 新 `location` （Azure 区域）是在重新部署过程中将提供的参数。

## <a name="move"></a>移动

1. 在目标区域中创建新的资源组（或使用现有的资源组）。

2. 使用导出的模板，按照使用 Azure 门户[从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)中的步骤操作。 你还可以使用[Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)、 [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)或[REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)。

3. 有关移动相关资源（例如[Azure 存储帐户](../storage/common/storage-account-move.md)）的指南，请参阅[跨区域移动 Azure 资源](../azure-resource-manager/management/move-region.md)主题中列出的各个服务的指南。

## <a name="verify"></a>}

1. 部署完成后，请测试应用程序终结点以验证应用程序的功能。

2. 还可以通过使用[Azure Service Fabric 网格 CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)检查应用程序状态（[az 网格应用显示](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)）并查看应用程序日志和（[az 网格代码包日志](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)）命令，来验证应用程序的状态。

## <a name="commit"></a>立即

确认目标区域中 Service Fabric 网格应用程序的等效功能后，将入口控制器（例如，[应用程序网关](../application-gateway/redirect-overview.md)）配置为将流量重定向到新应用程序。

## <a name="clean-up-source-resources"></a>清理源资源

若要完成移动 Service Fabric 网格应用程序，请[删除源应用程序和/或父资源组](../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>后续步骤

* [跨区域移动 Azure 资源](../azure-resource-manager/management/move-region.md)
* [支持跨区域移动 Azure 资源](../azure-resource-manager/management/region-move-support.md)
* [将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [对资源的移动操作支持](../azure-resource-manager/management/move-support-resources.md
)
