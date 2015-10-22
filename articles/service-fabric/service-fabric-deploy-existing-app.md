<properties
   pageTitle="在 Azure Service Fabric 中部署现有应用程序"
   description="有关如何打包现有应用程序以便可以在 Azure Service Fabric 群集上部署它的演练"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/06/2015"
   wacn.date=""/>

# 在 Azure Service Fabric 中部署现有应用程序

Azure Service Fabric 可用于部署现有应用程序。例如，可以“打包”Azure Web 或辅助角色上当前运行的应用程序，以便在 Service Fabric 群集上进行部署。
在 Service Fabric 群集上运行的现有应用程序可受益于运行状况监视和 ALM（应用程序生命周期管理）等功能，因此它是一个完全受支持的重要方案。
本教程介绍采用现有应用程序并将其打包所涉及的过程和基本概念。
本文概述了该过程，我们将跟踪有关如何利用现有应用程序（例如 node.js 或 Java）应用并将其打包以便可以承载在 Service Fabric 群集上的特定示例。

## 应用程序和服务清单文件的简要概述

在我们可以开始并了解如何打包现有应用程序之前，我们需要阅读 Service Fabric 部署模型的简要介绍。
Service Fabric 部署模型主要依赖两个文件：


* 应用程序清单。应用程序清单用于描述该应用程序并列出撰写该应用程序的服务以及其他参数（如实例数量），这些参数用于定义应该如何部署这些服务。在 Service Fabric 世界中，应用程序是“可升级的单元”。在由平台管理潜在故障（和潜在回滚）的情况下，应用程序可以升级为单个单元，以保证升级过程完全成功，如果该过程失败，则不会将该应用程序保留为未知/不稳定状态。

这是应用程序清单的一个示例：

	

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	  <ServiceManifestImport>
		<ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
		<ConfigOverrides />
	  </ServiceManifestImport>
	  <DefaultServices>
		<Service Name="actor2">
		  <StatelessService ServiceTypeName="actor2Type">
			<SingletonPartition />
		  </StatelessService>
		</Service>
	  </DefaultServices>
	</ApplicationManifest>

* 服务清单。服务清单描述服务的组件。它包括数据，例如服务的名称和类型（Service Fabric 用于管理该服务的信息）、其代码、配置和数据组件以及部署服务后可用于配置服务的一些其他参数。我们将不会探究服务清单中提供的所有不同参数的详细信息，我们将检查使现有应用程序能够在 Service Fabric 上运行所需的子集

这是服务清单的一个示例

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceManifest Name="actor2Pkg"
	                 Version="1.0.0.0"
	                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
	                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
	                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	  <ServiceTypes>
	    <StatelessServiceType ServiceTypeName="actor2Type" />
	  </ServiceTypes>
	
	  <CodePackage Name="Code" Version="1.0.0.0">
	    <EntryPoint>
	      <ExeHost>
	        <Program>actor2.exe</Program>
	      </ExeHost>
	    </EntryPoint>
	  </CodePackage>
	
	  <ConfigPackage Name="Config" Version="1.0.0.0" />
	
	  <Resources>
	    <Endpoints>
	      <Endpoint Name="ServiceEndpoint" />
	    </Endpoints>
	  </Resources>
	</ServiceManifest>
	
## 应用程序包文件结构
为了部署使用 powershell cmdlet 的应用程序，应用程序需要遵循预定义的目录结构。

\\applicationmanifest.xml
\\MyServicePkg
			\\servicemanifest.xml
			\\code
			\\config
			\\data

根目录包含定义该应用程序的 applicationmanifest.xml 文件。应用程序中包含的每个服务的子目录用于包含该服务需要的所有项目：servicemanifest.xml 以及通常三个目录：

* 代码：包含服务代码
* 配置：包含一个 settings.xml 文件（必要时还将包含其他文件），服务可以在运行时访问该文件以检索特定的配置设置。
* 数据：用于存储服务可能需要的其他本地数据的其他目录。注意：数据目录应仅用于存储临时数据，如果需要对服务进行重新定位（例如在故障转移期间），则 Service Fabric 不会复制对数据目录所做的更改。 

注意：你可以对代码、配置和数据目录使用任意目录名称。你应该需要确保在 ApplicationManifest 文件中使用相同的值。

## 打包现有应用的过程
打包现有应用程序的过程基于以下步骤进行：

* 创建包目录结构
* 添加应用程序的代码和配置文件
* 更新服务清单文件
* 更新应用程序清单


### 创建包目录结构
你可以通过按上文所述创建目录结构来开始。我创建了一个目录，并从我之前在 Visual Studio 中创建的现有项目中复制应用程序和服务清单。

![][1] ![][2]


### 添加应用程序的代码和配置文件
创建目录结构后，你可以在代码和配置目录下添加应用程序的代码和配置文件。还可以在代码或配置目录下创建其他目录或子目录。Service Fabric 对应用程序根目录下的内容执行了 xcopy，因此除创建代码和设置这两个顶级目录以外没有其他任何预定义结构可以使用（但如有需要也可以选取其他名称；有关详细信息，请参见下一节内容）。

!Azure Note：确保包含应用程序需要的所有文件/依赖项。Service Fabric 将复制群集中所有节点上的应用程序包的内容，将在群集中部署应用程序的服务。包应包含应用程序运行所需的全部代码。建议不要假定已安装依赖项。

### 编辑服务清单文件
下一步是编辑服务清单文件以包含如下信息：
* 服务类型的名称。这是 Service Fabric 用来标识服务的“ID”
* 用于启动应用程序 (ExeHost) 的命令
* 为了安装/配置应用程序而需要运行的任何脚本 (SetupEntrypoint

这是“打包”node.js 应用程序的 `servicemanifest.xnml` 文件的一个示例：

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
	                 Version="1.0.0.0"
	                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
	                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
	                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	  <ServiceTypes>
	    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
	  </ServiceTypes>
	  <CodePackage Name="Code" Version="1.0.0.0">
	    <EntryPoint>
	      <ExeHost>
	        <Program>node.exe</Program>
	        <Arguments>server.js</Arguments>
	        <WorkingFolder>CodeBase</WorkingFolder>
	      </ExeHost>
	    </EntryPoint>
	  </CodePackage>
	  <ConfigPackage Name="Config" Version="1.0.0.0"/>
	  <Resources>
	    <Endpoints>
	      <Endpoint Name="ServiceEndpoint" />
	    </Endpoints>
	  </Resources>
	</ServiceManifest>


我们先了解一下你需要更新的文件的不同部分：

### ServiceTypes：

```
<ServiceTypes>
<StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

* 你可以选择你想用于 `ServiceTypeName` 的任何名称，在 `applicationmanifest.xml` 中使用此值来标识该服务。
* 你需要指定 `UserImplicitHost = "true"`。此属性告知 Service Fabric 服务基于自包含的应用，因此它只需要将其作为进程启动并监视其运行状况。


### CodePackage 
CodePackage 指定服务代码的位置（和版本）。


	<CodePackage Name="Code" Version="1.0.0.0">


`Name` 元素用于指定包含服务代码的应用程序包中的目录的名称。`CodePackage` 还具有 `version` 属性，该属性可用于指定代码的版本，并且有可能用于通过利用 Service Fabric 的应用程序生命周期管理基础结构来升级服务代码。


### 入口点


	<EntryPoint>
	  <ExeHost>
	    <Program>node.exe</Program>
	    <Arguments>server.js</Arguments>
	    <WorkingFolder>CodeBase</WorkingFolder>
	  </ExeHost>
	</EntryPoint>


服务清单文件中的 `Entrypoint` 元素用于指定如何启动该服务。`ExeHost` 元素指定应用于启动该服务的可执行文件（和参数）。

* `Program`：指定为启动服务应执行的可执行文件的名称。 
* `Arguments`：它指定应传递到可执行文件的参数。它可以是带有实参的参数列表。
* `WorkingFolder`：它指定要启动的进程的工作目录。你可以指定两个值：
	* `CodeBase`：工作目录将设置为应用程序包中的代码目录（结构中的 `Code` 目录如下所示）
	* `CodePackage`：工作目录将设置为应用程序包的根目录 (`MyServicePkg`) `WorkingDirectory` 元素可用于设置正确的工作目录，以便应用程序或初始化脚本可以使用相对路径。你还可以为 `WorkingFolder` 元素 (`Work`) 指定另一个值，但对于使用现有应用程序的方案不是非常有用。


```

\applicationmanifest.xml
\MyServicePkg

			\servicemanifest.xml
			\code
				 \bin
				  \ ...
			\config
			\data
			\...

```


#### 安装入口点

	<SetupEntryPoint>
	    <ExeHost>
	        <Program>scripts\myAppsetup.cmd</Program>
	    </ExeHost>
	</SetupEntryPoint>


`SetupEntrypoint` 用于指定在启动服务代码前应执行的任何可执行文件或批处理文件。它是可选元素，因此如果不需要任何初始化/安装，则不需要包含它。每次重新启动该服务时，都将执行入口点。只有一个 SetupEntrypoint，因此如果应用程序的安装/配置需要多个脚本，则安装/配置脚本需要捆绑到单个批处理文件上。比如 `Entrypoint` 元素，`SetupEntrypoint` 可以执行任何类型的文件：可执行文件、批处理文件、powershell cmdlet。
在上述示例中，`SetupEntrypoint` 基于代码目录脚本子目录中的批处理文件 myAppsetup.cmd（假设 `WorkingDirectory` 元素设置为 `Code`）。

## 应用程序清单文件

配置 `servicemanifest.xml` 文件之后，你需要对 `applicationmanifest.xml` 文件进行一些更改，以确保使用正确的服务类型和名称。

	<ServiceManifestImport>
		<ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
	</ServiceManifestImport>
	<DefaultServices>
	<Service Name="actor2">
	  <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
	  </StatelessService>
	</Service>
	</DefaultServices>

### ServiceManifestImport

在 `ServiceManifestImport` 中，你可以指定一个或多个希望包含在应用中的服务。使用指定 `servicemanifest.xml` 文件所在目录的名称的 `ServiceManifestName` 来引用服务。

	<ServiceManifestImport>
		<ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
	</ServiceManifestImport>

### DefaultServices

应用程序清单文件中的 `DefaultServices` 元素用于定义某些服务属性。

	<DefaultServices>
	<Service Name="actor2">
	  <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
	  </StatelessService>
	</Service>


* `ServiceTypeName` 用作服务的“ID”。在迁移现有应用程序的上下文中，`ServiceTypeName` 只需要是服务的唯一标识。
* `StatelessService`：Service Fabric 支持两种类型的服务：无状态服务和有状态服务。在迁移现有应用程序的情况下，该服务是无状态服务，因此应始终使用 `StatelessService`。

Service Fabric 服务可以在各种“配置”中部署，例如，可以将其作为单个或多个实例部署，或者可以将其以这样一种方式部署：在 Service Fabric 群集的每个节点上都有一个服务实例。
在 `applicationmanifest.xml` 文件中，可以指定希望部署应用程序的方式

* `InstanceCount`：用于指定应在 Service Fabric 群集中启动的服务实例的数量。你可以设置 `InstanceCount` 值，具体取决于要部署的应用程序的类型。最常见的两种方案是：
	* `InstanCount = "1"`：在这种情况下，在群集上将仅部署一个服务实例。Service Fabric 的计划程序确定将在哪一个节点上部署服务。单个实例计数也可用于需要不同配置的应用程序（如果它们在多个实例上运行）。在这种情况下，更容易在同一应用程序清单文件中定义多个服务和使用 `InstanceCount = "1"`。因此，最终结果将为具有同一服务的多个实例，但每个实例都有特定的配置。仅当目标是拥有完全相同的配置的多个实例时，`InstanceCount` 的值大于 1 才有意义。
	* `InstanceCount ="-1"`：在这种情况下，将在 Service Fabric 群集中的每个节点上部署一个服务实例。最终结果将为群集中的每个节点具有一个（且只有一个）服务实例。这是前端应用程序（不包括 REST 终结点）的有用配置，因为客户端应用程序只需“连接到”群集中的任何节点以便使用该终结点。例如，当 Service Fabric 群集的所有节点都连接到负载平衡器时，还可以使用此配置，以便客户端流量可以分布在在群集中的所有节点上运行的服务上。


### 测试
现有应用程序非常有用的功能是能够看到控制台日志，以查明应用程序和配置脚本是否未显示任何错误。
可以使用 `ConsoleRedirection` 元素在 `servicemanifest.xml` 文件中配置控制台重定向

	<EntryPoint>
	  <ExeHost>
	    <Program>node.exe</Program>
	    <Arguments>server.js</Arguments>
	    <WorkingFolder></WorkingFolder>
	    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
	  </ExeHost>
	</EntryPoint>


* `ConsoleRedirection` 可用于将控制台输出（stdout 和 stderr）重定向至工作目录，以便可以将它们用于验证在 Service Fabric 群集中安装或执行应用程序期间没有错误。
	* `FileRetentionCount` 确定保存在工作目录中的文件的数量。例如，值 5 表示前 5 个执行的日志文件存储在工作目录中。
	* `FileMaxSizeInKb` 指定日志文件的最大大小。 

日志文件将保存在服务的其中一个工作目录上，为了确定文件的位置，你需要使用 Service Fabric 资源管理器来确定服务在哪一个节点中运行以及当前正在使用的工作目录。

在 Service Fabric 资源管理器中，确定服务在其中运行的节点

![][3]

知道服务当前在其中运行的节点后，就可以找出使用的工作目录

![][4]

选择服务的名称时，可以在右侧面板中看到服务代码和设置的存储位置

![][5]

如果单击“磁盘位置”字段中的链接，就可以访问运行服务的目录。

![][6]

日志目录包含所有日志文件。

注意：此示例演示在群集中运行的单个服务实例案例。如果有多个实例，则可能需要检查运行服务的所有节点上的日志文件。

 
## 后续步骤
我们正在开发可用于打包现有应用程序的工具，打包应用程序时只需在该应用的目录结构的根目录处指向它。工具将负责生成清单文件，并配置在 Service Fabric 服务中“转换”应用程序所需的基本设置。


<!--Image references-->
[1]: ./media/service-fabric-deploy-an-existing-app/directory-structure-1.PNG
[2]: ./media/service-fabric-deploy-an-existing-app/directory-structure-2.PNG
[3]: ./media/service-fabric-deploy-an-existing-app/service-node-1.PNG
[4]: ./media/service-fabric-deploy-an-existing-app/service-node-2.PNG
[5]: ./media/service-fabric-deploy-an-existing-app/service-node-3.PNG
[6]: ./media/service-fabric-deploy-an-existing-app/service-node-4.PNG

	

<!---HONumber=74-->