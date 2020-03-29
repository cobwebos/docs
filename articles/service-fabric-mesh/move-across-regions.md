---
title: 将服务结构网格应用程序移动到其他区域
description: 通过将当前模板的副本部署到新的 Azure 区域，可以移动服务结构网格资源。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908158"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>将服务结构网格应用程序移动到其他 Azure 区域

本文介绍如何将服务结构网格应用程序及其资源移动到其他 Azure 区域。 出于多种原因，您可以将资源移动到另一个区域。 例如，为了响应中断，获取仅在特定区域可用的功能或服务，以满足内部策略和治理要求，或响应容量规划要求。

 [服务结构网格不支持](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh)在 Azure 区域之间直接移动资源的能力。 但是，可以通过将当前 Azure 资源管理器模板的副本部署到新目标区域，然后将入口流量和依赖项重定向到新创建的 Service Fabric Mesh 应用程序，间接移动资源。

## <a name="prerequisites"></a>先决条件

* 入口控制器（如[应用程序网关](https://docs.microsoft.com/azure/application-gateway/)），作为客户端和服务结构网格应用程序之间路由流量的中间
* 目标 Azure 区域中的服务结构网格（预览）可用性`westus` `eastus`（ `westeurope`、 或 ）

## <a name="prepare"></a>准备

1. 通过导出 Azure 资源管理器模板和来自最新部署的参数，获取服务结构网格应用程序的当前状态的"快照"。 为此，请使用 Azure 门户在[部署后按照导出模板](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)中的步骤操作。 也可以使用 [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) 或 [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)。

2. 如果适用，[将导出同一资源组中的其他资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group)，以便重新部署到目标区域中。

3. 查看（如果需要）导出的模板，以确保现有属性值是要在目标区域中使用的属性值。 新的`location`（Azure 区域）是重新部署期间将提供的参数。

## <a name="move"></a>移动

1. 在目标区域中创建新的资源组（或使用现有资源组）。

2. 使用导出的模板，请按照使用 Azure 门户[从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)中的步骤进行操作。 也可以使用 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)、[Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell) 或 [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)。

3. 有关移动相关资源（如[Azure 存储帐户](../storage/common/storage-account-move.md)）的指导，请参阅主题"[跨区域移动 Azure 资源"](../azure-resource-manager/management/move-region.md)下列出的单个服务的指导。

## <a name="verify"></a>Verify

1. 部署完成后，测试应用程序终结点以验证应用程序的功能。

2. 还可以通过检查应用程序状态[（az mesh 应用显示](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)）并使用[Azure 服务结构网格 CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)查看应用程序日志和[（az 网格代码包日志](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)）命令来验证应用程序的状态。

## <a name="commit"></a>Commit

确认目标区域中的 Service Fabric Mesh 应用程序的等效功能后，配置入口控制器（例如[应用程序网关](../application-gateway/redirect-overview.md)），将流量重定向到新应用程序。

## <a name="clean-up-source-resources"></a>清理源资源

要完成服务结构网格应用程序的移动，[请删除源应用程序和/或父资源组](../azure-resource-manager/management/delete-resource-group.md)。

## <a name="next-steps"></a>后续步骤

* [跨区域移动 Azure 资源](../azure-resource-manager/management/move-region.md)
* [跨区域移动 Azure 资源的支持](../azure-resource-manager/management/region-move-support.md)
* [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [支持移动操作的资源](../azure-resource-manager/management/move-support-resources.md
)
