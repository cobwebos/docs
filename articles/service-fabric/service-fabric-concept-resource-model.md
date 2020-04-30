---
title: Azure Service Fabric 应用程序资源模型
description: 本文概述如何使用 Azure 资源管理器管理 Azure Service Fabric 应用程序。
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682646"
---
# <a name="service-fabric-application-resource-model"></a>Service Fabric 应用程序资源模型

你有多个选项可用于在 Service Fabric 群集上部署 Azure Service Fabric 应用程序。 建议使用 Azure 资源管理器。 如果使用资源管理器，则可以使用 JSON 描述应用程序和服务，然后将它们部署到与群集相同的资源管理器模板中。 不同于使用 PowerShell 或 Azure CLI 来部署和管理应用程序，如果使用资源管理器，则不必等待群集准备就绪;应用程序注册、预配和部署都可以在一步中进行。 使用资源管理器是在群集中管理应用程序生命周期的最佳方式。 有关详细信息，请参阅[最佳做法：基础结构即代码](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)。

在资源管理器中将应用程序作为资源进行管理可以帮助你在这些领域中获得改进：

* 审核跟踪：资源管理器审核每个操作，并保留详细的活动日志。 活动日志可帮助你跟踪对应用程序和群集所做的任何更改。
* 基于角色的访问控制：通过使用相同的资源管理器模板，你可以管理对群集和部署在群集上的应用程序的访问。
* 管理效率：使用资源管理器提供用于管理群集和关键应用程序部署的单个位置（Azure 门户）。

本文档将介绍以下操作：

> [!div class="checklist"]
>
> * 使用资源管理器部署应用程序资源。
> * 使用资源管理器升级应用程序资源。
> * 删除应用程序资源。

## <a name="deploy-application-resources"></a>部署应用程序资源

使用资源管理器应用程序资源模型来部署应用程序及其服务所采用的高级步骤如下：
1. 打包应用程序代码。
1. 上载包。
1. 以应用程序资源的形式引用包在资源管理器模板中的位置。 

有关详细信息，请查看[将应用程序打包](service-fabric-package-apps.md#create-an-sfpkg)。

然后，创建一个资源管理器模板，使用应用程序详细信息更新参数文件，并在 Service Fabric 群集上部署该模板。 [探索示例](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。

### <a name="create-a-storage-account"></a>创建存储帐户

若要从资源管理器模板部署应用程序，必须有一个存储帐户。 存储帐户用于暂存应用程序映像。 

你可以重复使用现有的存储帐户，也可以创建新的存储帐户来过渡应用程序。 如果使用现有存储帐户，则可以跳过此步骤。 

![创建存储帐户][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>配置存储帐户

创建存储帐户后，可以创建一个可在其中暂存应用程序的 blob 容器。 在 Azure 门户中，请前往要在其中存储应用程序的 Azure 存储帐户。 选择 " **blob** > " "**添加容器**"。 

可以通过将公共访问级别设置为 "**专用**" 来保护群集中的资源。 可以通过多种方式授予访问权限：

* 使用[Azure Active Directory](../storage/common/storage-auth-aad-app.md)授予对 blob 和队列的访问权限。
* [在 Azure 门户中使用 RBAC](../storage/common/storage-auth-aad-rbac-portal.md)授予对 Azure blob 和队列数据的访问权限。
* 使用[共享访问签名](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)委托访问权限。

以下屏幕截图中的示例使用对 blob 的匿名读取访问。

![创建 Blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>在存储帐户中暂存应用程序

在部署应用程序之前，必须在 blob 存储中暂存应用程序。 在本教程中，我们将手动创建应用程序包。 请记住，此步骤可以自动执行。 有关详细信息，请参阅[将应用程序打包](service-fabric-package-apps.md#create-an-sfpkg)。 

在本教程中，我们将使用[投票示例应用程序](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在 Visual Studio 中，右键单击**投票**项目，然后选择 "**包**"。

   ![将应用程序打包][PackageApplication]  
1. 请参阅 *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug*目录。 将内容压缩到名为 "*投票 .zip*" 的文件中。 *Applicationmanifest.xml*文件应位于 zip 文件中的根位置。

   ![Zip 应用程序][ZipApplication]  
1. 重命名该文件，将扩展名从 .zip 更改为 *.sfpkg*。

1. 在 Azure 门户的存储帐户的 "**应用**" 容器中，选择 "**上传**"，然后上传 **.sfpkg**。 

   ![上传应用包][UploadAppPkg]

现在，应用程序现已暂存，你可以创建资源管理器模板来部署应用程序。

### <a name="create-the-resource-manager-template"></a>创建 资源管理器模板

示例应用程序包含可用于部署应用程序的[Azure 资源管理器模板](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。 模板文件名称为*UserApp*和*UserApp*。

> [!NOTE]
> 必须使用群集名称更新 *UserApp.Parameters.json* 文件。
>
>

| 参数              | 说明                                 | 示例                                                      | 说明                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 要部署到的群集的名称 | sf-cluster123                                                |                                                              |
| application            | 应用程序的名称                 | Voting                                                       |
| applicationTypeName    | 应用程序的类型名称           | VotingType                                                   | 必须匹配 Applicationmanifest.xml                 |
| applicationTypeVersion | 应用程序类型的版本         | 1.0.0                                                        | 必须匹配 Applicationmanifest.xml                 |
| serviceName            | 服务的名称         | Voting~VotingWeb                                             | 必须采用 ApplicationName~ServiceType 格式            |
| serviceTypeName        | 服务的类型名称                | VotingWeb                                                    | 必须匹配 Servicemanifest.xml                 |
| appPackageUrl          | 应用程序的 Blob 存储 URL     | https：\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob 存储中应用程序包的 URL （本文后面将介绍设置 URL 的过程） |

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

运行**AzResourceGroupDeployment** cmdlet，将应用程序部署到包含群集的资源组：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>使用资源管理器升级 Service Fabric 应用程序

出于以下原因之一，你可以将已部署的应用程序升级到 Service Fabric 群集：

* 已将一个新服务添加到应用程序。 将服务添加到应用程序时，必须将服务定义添加到*service-manifest*和*application-manifest*文件中。 若要反映应用程序的新版本，还必须在[UserApp](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)中将应用程序类型版本从1.0.0 更改为1.0.1：

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

* 现有服务的新版本将添加到应用程序中。 示例包括应用程序代码更改以及应用程序类型版本和名称的更新。 对于此升级，请更新 UserApp，如下所示：

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>删除应用程序资源

若要删除使用资源管理器中的应用程序资源模型部署的应用程序，请执行以下操作：

1. 使用[AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) cmdlet 获取应用程序的资源 ID：

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. 使用[AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) cmdlet 删除应用程序资源：

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>后续步骤

获取有关应用程序资源模型的信息：

* [在 Service Fabric 中对应用程序建模](service-fabric-application-model.md)
* [Service Fabric 应用程序和服务清单](service-fabric-application-and-service-manifests.md)
* [最佳做法：基础结构即代码](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [将应用程序和服务作为 Azure 资源进行管理](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
