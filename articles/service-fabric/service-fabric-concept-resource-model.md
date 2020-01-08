---
title: Azure Service Fabric 应用程序资源模型
description: 本文概述如何使用 Azure 资源管理器管理 Azure Service Fabric 应用程序
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: b3cf0b8f21565a8d51b16ff6c8b4c52bbfe8edc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464800"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>什么是 Service Fabric 应用程序资源模型？
建议通过 Azure 资源管理器将 Service Fabric 应用程序部署到 Service Fabric 群集。 此方法可以在 JSON 中描述应用程序和服务，并将其部署在与群集相同的资源管理器模板中。 与通过 PowerShell 或 Azure CLI 部署和管理应用程序相反，无需等待群集准备就绪。 只需执行一步操作，即可完成注册、预配和部署应用程序的整个过程。 这是在群集中管理应用程序生命周期的最佳做法。 有关详细信息，请查看[最佳实践](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)。

在适当情况下，将应用程序作为资源管理器资源进行管理可改进：
* 审核线索：资源管理器审核所有操作，并记录详细的活动日志，有助于跟踪对这些应用程序和群集做出的任何更改。
* 基于角色的访问控制：管理群集的访问权限以及部署在群集上的应用程序可以通过相同的资源管理器模板来完成。
* Azure 资源管理器（通过 Azure 门户）成为管理群集和关键应用程序部署的一站式管理。

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>通过 Azure 资源管理器 Service Fabric 应用程序生命周期 
在本文档中，你将学习如何：

> [!div class="checklist"]
> * 使用 Azure 资源管理器部署应用程序资源 
> * 使用 Azure 资源管理器升级应用程序资源
> * 删除应用程序资源

## <a name="deploy-application-resources-using-azure-resource-manager"></a>使用 Azure 资源管理器部署应用程序资源  
若要使用 Azure 资源管理器应用程序资源模型部署应用程序及其服务，需要打包应用程序代码，上传包，然后在 Azure 资源管理器模板中将包位置作为应用程序进行引用资源. 有关详细信息，请查看[将应用程序打包](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)。
          
然后，创建 Azure 资源管理器模板，使用应用程序详细信息更新参数文件，并将其部署到 Service Fabric 群集上。 请参阅[此处](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)的示例。

### <a name="create-a-storage-account"></a>创建存储帐户 
从资源管理器模板部署应用程序需要使用存储帐户暂存应用程序映像。 你可以重新使用现有存储帐户，也可以创建新的存储帐户来暂存你的应用程序。 如果要使用现有的存储帐户，可以跳过此步骤。 

![创建存储帐户][CreateStorageAccount]

### <a name="configure-storage-account"></a>配置存储帐户 
创建存储帐户后，需要创建一个可在其中暂存应用程序的 blob 容器。 在 Azure 门户中，导航到要存储应用程序的存储帐户。 选择 " **blob** " 边栏选项卡，并单击 "**添加容器**" 按钮。 可以通过将公共访问级别设置为 "专用" 来保护群集中的资源。 可以通过多种方式授予访问权限：
* [使用 Azure Active Directory 授予对 blob 和队列的访问权限](../storage/common/storage-auth-aad-app.md)
* [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](../storage/common/storage-auth-aad-rbac-portal.md)
* [使用共享访问签名（SAS）委派访问权限](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature
)

 在此示例中，我们将继续使用对 blob 的匿名读取访问。

![创建 blob][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>在存储帐户中暂存应用程序
在部署应用程序之前，必须在 blob 存储中暂存应用程序。 在本教程中，我们将手动创建应用程序包，但可以自动执行此步骤。  有关详细信息，请查看[将应用程序打包](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)。 在下面的步骤中，将使用[投票示例应用程序](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在 Visual Studio 中，右键单击投票项目，然后选择 "包"。   
![包应用程序][PackageApplication]  
2. 打开刚刚创建的 **.\service-fabric-dotnet-quickstart\Voting\pkg\Debug**目录，并将内容压缩到名为 "**投票**" 的文件中，以便 applicationmanifest.xml 位于 zip 文件的根目录下。  
![Zip 应用程序][ZipApplication]  
3. 将该文件的扩展名从 .zip 重命名为 **.sfpkg**。
4. 在 Azure 门户的存储帐户的 "**应用**" 容器中，单击 "**上传**" 并上传 **.sfpkg**。  
![上传应用程序包][UploadAppPkg]

应用程序现已暂存。 现在，我们已准备好创建 Azure 资源管理器模板来部署应用程序。      
   
### <a name="create-the-azure-resource-manager-template"></a>创建 Azure 资源管理器模板
示例应用程序包含可用于部署应用程序的[Azure 资源管理器模板](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。 模板文件被命名为**UserApp**和**UserApp**。 

> [!NOTE] 
> 必须将**UserApp**文件更新为你的群集的名称。
>
>

| 参数              | Description                                 | 示例                                                      | 注释                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 要部署到的群集的名称 | sf-cluster123                                                |                                                              |
| application            | 应用程序的名称                 | 否决                                                       |
| applicationTypeName    | 应用程序的类型名称           | VotingType                                                   | 必须匹配 Applicationmanifest.xml 中的内容                 |
| applicationTypeVersion | 应用程序类型的版本         | 1.0.0                                                        | 必须匹配 Applicationmanifest.xml 中的内容                 |
| serviceName            | 服务的服务名称。         | 投票 ~ VotingWeb                                             | 必须采用 ApplicationName ~ ServiceType 格式            |
| serviceTypeName        | 服务的类型名称                | VotingWeb                                                    | 必须与 Servicemanifest.xml 中的内容匹配                 |
| appPackageUrl          | 应用程序的 blob 存储 URL     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob 存储中应用程序包的 URL （如下所述的过程） |
       
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
若要部署应用程序，请运行 AzResourceGroupDeployment 以部署到包含群集的资源组。
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>使用 Azure 资源管理器升级 Service Fabric 应用程序
由于以下原因，已部署到 Service Fabric 群集的应用程序将进行升级：

1. 新服务将添加到应用程序。 必须将服务定义添加到 service-manifest 和 application-manifest 文件中。 然后，若要反映应用程序的新版本，需要将应用程序类型版本从1.0.0 更新为 1.0.1 [UserApp](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)。

    ```
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
2. 现有服务的新版本将添加到应用程序中。 这涉及应用程序代码更改以及应用类型版本和名称的更新。

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>删除应用程序资源
可以使用以下步骤从群集中删除使用 Azure 中的应用程序资源模型部署的应用程序资源管理器

1) 使用[AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)命令获取应用程序的资源 id  

    #### <a name="get-resource-id-for-application"></a>获取应用程序的资源 ID
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) 使用[AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)删除应用程序资源  

    #### <a name="delete-application-resource-using-its-resource-id"></a>使用其资源 ID 删除应用程序资源
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>后续步骤
获取有关应用程序资源模型的信息：

* [在 Service Fabric 中对应用程序建模](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric 应用程序和服务清单](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>另请参阅
* [最佳做法](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [将应用程序和服务作为 Azure 资源进行管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
