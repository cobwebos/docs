---
title: Azure Service Fabric 应用程序资源模型
description: 本文概述了使用 Azure 资源管理器管理 Azure 服务结构应用程序。
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 69c10b0e9d3b7c29122c8432ab1e4bc06d3a3120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481110"
---
# <a name="service-fabric-application-resource-model"></a>服务结构应用程序资源模型

在 Service Fabric 群集上部署 Azure 服务结构应用程序有多个选项。 我们建议使用 Azure 资源管理器。 如果使用资源管理器，则可以在 JSON 中描述应用程序和服务，然后将它们部署在群集相同的资源管理器模板中。 与使用 PowerShell 或 Azure CLI 部署和管理应用程序不同，如果使用资源管理器，则不必等待群集准备就绪;因此，无需等待群集准备就绪。应用程序注册、预配和部署都可以在一个步骤中发生。 使用资源管理器是管理群集中应用程序生命周期的最佳方式。 有关详细信息，请参阅[最佳实践：基础结构作为代码](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)。

将应用程序作为资源管理器中的资源管理可帮助您在以下方面获得改进：

* 审核跟踪：资源管理器审核每个操作并保留详细的活动日志。 活动日志可以帮助您跟踪对应用程序和群集所做的任何更改。
* 基于角色的访问控制：您可以使用同一资源管理器模板管理对群集和群集上部署的应用程序的访问。
* 管理效率：使用资源管理器为您提供一个位置（Azure 门户），用于管理群集和关键应用程序部署。

本文档将介绍以下操作：

> [!div class="checklist"]
>
> * 使用资源管理器部署应用程序资源。
> * 使用资源管理器升级应用程序资源。
> * 删除应用程序资源。

## <a name="deploy-application-resources"></a>部署应用程序资源

使用资源管理器应用程序资源管理器资源管理器模型部署应用程序及其服务所采用的高级步骤包括：
1. 打包应用程序代码。
1. 上传包。
1. 将包在资源管理器模板中的位置作为应用程序资源引用。 

有关详细信息，请查看[将应用程序打包](service-fabric-package-apps.md#create-an-sfpkg)。

然后，创建资源管理器模板，使用应用程序详细信息更新参数文件，并在 Service Fabric 群集上部署该模板。 [探索示例](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。

### <a name="create-a-storage-account"></a>创建存储帐户

要从资源管理器模板部署应用程序，您必须具有存储帐户。 存储帐户用于暂存应用程序映像。 

您可以重用现有存储帐户，也可以创建新存储帐户来暂存应用程序。 如果使用现有存储帐户，则可以跳过此步骤。 

![创建存储帐户][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>配置存储帐户

创建存储帐户后，将创建一个 Blob 容器，其中可以暂存应用程序。 在 Azure 门户中，转到要存储应用程序的 Azure 存储帐户。 选择**Blob** > **添加容器**。 

可以通过将公共访问级别设置为**私有**，保护群集中的资源。 您可以通过多种方式授予访问权限：

* 使用[Azure 活动目录](../storage/common/storage-auth-aad-app.md)授权访问 Blob 和队列。
* 通过在[Azure 门户中使用 RBAC](../storage/common/storage-auth-aad-rbac-portal.md)来授予对 Azure blob 和队列数据的访问。
* 使用[共享访问签名](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)委派访问权限。

以下屏幕截图中的示例使用 blob 的匿名读取访问。

![创建 Blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>在存储帐户中暂存应用程序

在部署应用程序之前，必须在 Blob 存储中暂存应用程序。 在本教程中，我们手动创建应用程序包。 请记住，此步骤可以自动执行。 有关详细信息，请参阅[打包应用程序](service-fabric-package-apps.md#create-an-sfpkg)。 

在本教程中，我们使用[投票示例应用程序](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在可视化工作室中，右键单击**投票**项目，然后选择 **"包**"。

   ![将应用程序打包][PackageApplication]  
1. 转到 *._服务-结构-点-快速入门\投票\pkg_调试*目录。 将内容压缩到名为 *"投票.zip"* 的文件中。 *应用程序清单.xml*文件应位于 zip 文件中的根目录。

   ![Zip 应用程序][ZipApplication]  
1. 重命名文件以将扩展名从 .zip 更改为 *.sfpkg*。

1. 在 Azure 门户中，在存储帐户**的应用**容器中，选择 **"上载**"，然后上载**投票.sfpkg**。 

   ![上传应用包][UploadAppPkg]

现在，应用程序现在暂存，您可以创建资源管理器模板来部署应用程序。

### <a name="create-the-resource-manager-template"></a>创建 资源管理器模板

示例应用程序包含可用于部署应用程序的[Azure 资源管理器模板](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。 模板文件名是*UserApp.json*和*UserApp.parameter.json*。

> [!NOTE]
> 必须使用群集名称更新 *UserApp.Parameters.json* 文件。
>
>

| 参数              | 说明                                 | 示例                                                      | 注释                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 要部署到的群集的名称 | sf-cluster123                                                |                                                              |
| application            | 应用程序的名称                 | Voting                                                       |
| applicationTypeName    | 应用程序的类型名称           | VotingType                                                   | 必须匹配应用程序清单.xml                 |
| applicationTypeVersion | 应用程序类型的版本         | 1.0.0                                                        | 必须匹配应用程序清单.xml                 |
| serviceName            | 服务的名称         | Voting~VotingWeb                                             | 必须采用 ApplicationName~ServiceType 格式            |
| serviceTypeName        | 服务的类型名称                | VotingWeb                                                    | 必须匹配 ServiceManifest.xml                 |
| appPackageUrl          | 应用程序的 Blob 存储 URL     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | blob 存储中应用程序包的 URL（本文稍后将介绍设置 URL 的过程） |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>部署应用程序

运行**New-AzResourceGroup 部署**cmdlet 以将应用程序部署到包含群集的资源组：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>使用资源管理器升级服务结构应用程序

出于以下原因之一，可以升级已部署到 Service Fabric 群集的应用程序：

* 已将一个新服务添加到应用程序。 将服务添加到应用程序时，必须将服务定义添加到*服务-清单.xml*和*应用程序-清单.xml*文件中。 要反映应用程序的新版本，还必须在[UserApp.parameter.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)中将应用程序类型版本从 1.0.0 更改为 1.0.1：

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* 现有服务的新版本将添加到应用程序中。 示例包括应用程序代码更改和应用程序类型版本和名称的更新。 对于此升级，请更新 UserApp.parameters.json，如下所示：

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>删除应用程序资源

要删除在资源管理器中使用应用程序资源模型部署的应用程序，请执行以下规定：

1. 使用[Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) cmdlet 获取应用程序的资源 ID：

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. 使用["删除-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) cmdlet"删除应用程序资源：

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>后续步骤

获取有关应用程序资源模型的信息：

* [在 Service Fabric 中对应用程序建模](service-fabric-application-model.md)
* [Service Fabric 应用程序和服务清单](service-fabric-application-and-service-manifests.md)
* [最佳实践：基础架构作为代码](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [将应用程序和服务作为 Azure 资源管理](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
