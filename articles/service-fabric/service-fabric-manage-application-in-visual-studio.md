<properties
   pageTitle="在 Visual Studio 中管理 Service Fabric 应用程序"
   description="你可以通过 Visual Studio 管理 Microsoft Azure Service Fabric 应用程序和服务。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# 在 Visual Studio 中管理 Service Fabric 应用程序

你可以通过 Visual Studio 管理 Microsoft Azure Service Fabric 应用程序和服务。[设置开发环境](/documentation/articles/service-fabric-setup-your-development-environment)之后，你可以使用 Visual Studio 创建 Service Fabric 应用程序、添加服务，或打包、注册和在本地开发群集中部署应用程序。

若要管理你的 Service Fabric 应用程序，在解决方案资源管理器中，右键单击你的应用程序项目。

![通过右键单击应用程序项目来管理你的 Service Fabric 应用程序][manageservicefabric]

## 部署 Service Fabric 应用程序

部署 Service Fabric 应用程序会将以下步骤合并为一个简单的操作。

1. 创建应用程序包
2. 将应用程序包上载到映像存储
3. 注册应用程序类型
4. 删除任何正在运行的应用程序实例
5. 创建新的应用程序实例

在 Visual Studio 中，你可以通过从“构建”菜单中选择部署解决方案来部署应用程序。通过按 **F5**，还可以部署你的应用程序并将调试器附加到所有应用程序实例。


## 向你的 Service Fabric 应用程序中添加服务

可以向你的应用程序中添加新的 Fabric 服务来扩展其功能。若要确保服务包含在你的应用程序包中，请通过“新 Fabric 服务...”菜单项添加服务。

![将新的 Fabric 服务添加到你的应用程序中][newservice]

选择要添加到你的应用程序的 Service Fabric 项目类型，并指定服务的名称。请参阅[为你的服务选择框架](/documentation/articles/service-fabric-choose-framework)，以帮助你决定要使用的服务类型。

![选择要添加到你的应用程序的 Fabric 服务项目类型][addserviceproject]

新的服务会添加到你的解决方案和现有应用程序包中。服务引用和默认的服务实例会添加到应用程序清单中。将在下次部署应用程序时创建并启动服务。

![新的服务会添加到应用程序清单中][newserviceapplicationmanifest]

## 打包 Service Fabric 应用程序

需要创建应用程序包，以便将应用程序及其服务部署到群集中。该包会组织应用程序清单、服务清单和特定布局的其他必要文件。Visual Studio 设置和管理“pkg”目录中应用程序项目的文件夹中的包。单击“包”创建或更新应用程序包。如果使用自定义 Powershell 脚本部署应用程序，你可能要执行此操作。

## 删除应用程序

你可以使用服务器资源管理器从本地群集中删除应用程序。此操作会还原上述部署步骤：

1. 删除任何正在运行的应用程序实例
2. 取消注册应用程序类型
3. 从映像存储中删除应用程序包

![删除应用程序](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

- [Service Fabric 应用程序模型](/documentation/articles/service-fabric-application-model)
- [Service Fabric 应用程序部署](/documentation/articles/service-fabric-deploy-remove-applications)
- [调试 Service Fabric 应用程序](/documentation/articles/service-fabric-debugging-your-application)
- [使用 Service Fabric 资源管理器可视化群集](/documentation/articles/service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png

<!---HONumber=74-->