
<!--preview portal-->
<properties
   pageTitle="从 Azure 门户创建 Service Fabric 群集 | Microsoft Azure"
   description="从 Azure 门户创建 Service Fabric 群集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/29/2016"
   wacn.date=""/>


# 从 Azure 门户创建 Service Fabric 群集

本页面帮助你设置 Azure Service Fabric 群集。订阅必须有足够的核心用于部署将构成此群集的 IaaS VM。


## 创建群集

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 单击“+ 新建”添加新的资源模板。在“全部”下面的“应用商店”中搜索你的模板，该模板名为“Service Fabric 群集”。

    a.单击顶层的“应用商店”。

    b.单击“全部”下面的“Fabric”，然后按 Enter。有时自动筛选器无法工作，因此请务必按 Enter。
    ![在 Azure 门户上搜索 Service Fabric 群集模板的屏幕截图。][SearchforServiceFabricClusterTemplate]

3. 从列表中选择“Service Fabric 群集”。

4. 导航到“Service Fabric 群集”边栏选项卡，然后单击“创建”并提供群集的详细信息。

5. 在“创建新的资源组”下面，为资源组指定与群集相同的名称。这样稍后就可以轻松找到它们，在尝试更改部署或删除群集时非常有用。

    >[AZURE.NOTE] 尽管你可以决定使用现有资源组，但最好还是创建新的资源组。这样可以轻松删除不需要的群集。

 	![创建新资源组的屏幕截图。][CreateRG]

6. 务必选择要将群集部署到的**订阅**，尤其是在拥有多个订阅时。

7. 从下拉列表中选择一个**位置**。默认值为“美国西部”。

8. 配置“节点类型”。节点类型相当于云服务中的角色。节点类型定义 VM 大小、VM 数目及其属性。群集可以有不只一个节点类型，但主节点类型（在门户定义的第一个节点类型）必须至少有 5 个 VM。配置节点类型：

	a.选择所需的 VM 大小/定价层。默认值为“D4 标准”，但如果只是要用此群集来测试自己的应用程序，也可以选择“D2”或任何较小的 VM。

	b.选择 VM 的数目。以后你可以增加或减少节点类型中的 VM 数目，但第一个节点类型必须至少有 5 个 VM。

	c.选择节点类型的名称（1 到 12 个字符，只能包含字母和数字）。

	d.选择 VM 远程桌面的“用户名”和“密码”。

	e.如果群集需要多个节点类型，请注意以下问题。（如果你打算只为群集部署单节点类型，请跳到步骤 9。）

	* 假设你要部署包含前端服务和后端服务的应用程序，并想将前端服务放在容量较小（A2、D2 等的 VM 大小）且拥有可连接到 Internet 的端口的 VM 上。但同时，你想将计算密集型后端服务放在容量较大（D4、D6、D12 等的 VM 大小）且不连接到 Internet 的 VM 上。

	* 尽管可以将这两种服务都放同一个节点类型上，但我们建议将它们分别放在群集中的两个节点类型上。每个节点类型都可以有不同的属性，例如 Internet 连接、VM 大小，以及能单独调整的 VM 数目。

	* 先定义一个至少包含 5 个 VM 的节点类型。其他节点类型可以至少包含 1 个 VM。

  	![创建节点类型的屏幕截图。][CreateNodeType]

9. 如果你打算立刻将应用程序部署到群集，请在“应用程序端口”节点类型（或创建的节点类型）上添加要为应用程序开放的端口。稍后可以再为节点类型添加端口，方法是修改与该此节点类型关联的负载平衡器。（添加探测，然后将探测添加到负载平衡器规则。） 立即执行此操作相对来说比较简单，因为门户自动化功能会将所需的探测和规则添加到负载平衡器。

	a.可以在服务清单中查找属于应用程序包的应用程序端口。转到每个应用程序，打开服务清单，然后记下应用程序与外界通信时所需的输入终结点。

	b.在“应用程序输入终结点”字段中添加所有端口（以逗号分隔）。TCP 客户端连接终结点的默认值为 19000，因此不需要指定该值。例如，示例应用程序 WordCount 需要打开端口 83。可以在应用程序包的 servicemanifest.xml 文件中找到该示例应用程序。（其中可能包含多个 servicemanifest.xml 文件。）

    c.大多数示例应用程序都使用端口 80 和 8081，因此如果你打算将示例部署到此群集，请添加这两个端口。
    ![端口][Ports]

10. 不需要配置“放置属性”，因为系统添加了“NodeTypeName”的默认放置属性。如果应用程序有需要，可以添加更多属性。

## 配置安全性

目前，Service Fabric 只支持通过 X509 证书保护群集。在开始此过程之前，必须先将证书上载到密钥保管库。有关如何执行此操作的详细信息，请参阅 [Service Fabric 群集安全性](service-fabric-cluster-security.md)。

保护群集的选项是可选的，但强烈建议这样做。如果选择不保护群集，请将“安全模式”切换为“无”。

有关安全注意事项和说明的文档，请参阅 [Service Fabric 群集安全性](service-fabric-cluster-security.md)。

![Azure 门户上安全配置的屏幕截图。][SecurityConfigs]

## 可选：配置诊断

默认情况下，已在群集上启用诊断，以帮助排查问题。如果你要禁用诊断，请执行以下操作：

1. 导航到“诊断配置”边栏选项卡。

2. 将“状态”切换为“关闭”。

## 可选：设置 Service Fabric 群集设置

可以使用这个高级选项来更改 Service Fabric 群集的默认设置。建议不要更改默认配置，除非你确定自己的应用程序或群集有此需要。

## 完成群集创建

如要完成群集创建过程，请单击“摘要”查看你提供的配置，或下载将用于部署群集的 Azure Resource Manager 模板。在提供所有必需的设置后，“创建”按钮将会启用，你只需单击它就能启动群集创建过程。

可以在通知栏中查看群集创建进度。（单击屏幕右上角状态栏附近的铃铛图标）。 如果在创建群集时曾经单击“固定到启动板”，现在会看到“部署 Service Fabric 群集”已固定到**开始**面板。

![显示“正在部署 Service Fabric 群集”的开始面板屏幕截图。][Notifications]

## 查看群集状态

部署完成后，可以在门户检查群集：

1. 转到“浏览”，然后单击“Service Fabric 群集”。

2. 查找你的群集并单击它。
  ![在门户中查找群集的屏幕截图。][BrowseCluster]

3. 现在仪表板将显示群集的详细信息，包括群集的公共 IP 地址。将鼠标光标移到“群集公共 IP 地址”上会显示剪贴板，单击它可以复制该 IP 地址。
  ![仪表板中群集详细信息的屏幕截图。][ClusterDashboard]

  群集仪表板边栏选项卡上的“节点监视器”部分显示运行正常和不正常的 VM 的数目。若要了解有关群集运行状况的详细信息，请参阅 [Service Fabric 运行状况模型简介](service-fabric-health-introduction.md)。

## 连接到群集并部署应用程序

创建群集后，可以连接到群集，并开始部署应用程序。首先，请在已安装 Service Fabric SDK 的计算机上启动 Windows PowerShell。然后，若要连接到群集，请根据创建的是安全群集还是不安全群集，运行以下 PowerShell 命令集之一：

- 选项 1：连接到不安全的群集。

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- 选项 2：连接到安全群集。

    1. 运行以下命令，以便在即将用于运行“Connect-serviceFabricCluster”PowerShell 命令的计算机上设置证书。

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. 运行以下 PowerShell 命令连接到安全群集。证书的详细信息与门户上提供的信息相同。

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        例如，上述 PowerShell 命令应该类似于以下内容：

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

现在你已创建连接，请运行以下命令来部署应用程序，但别忘了要先用计算机上适当的路径来替换以下所示的路径。以下示例将部署单词计数示例应用程序：

1. 将包复制到前面连接到的群集。

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. 向 Service Fabric 注册应用程序类型。

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. 针对刚刚注册的应用程序类型创建新实例。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. 现在，请打开所选的浏览器，然后连接到应用程序正在侦听的终结点。WordCount 示例应用程序的 URL 如下所示：

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 后续步骤

- [在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric 群集安全性](service-fabric-cluster-security.md)
- [Service Fabric 运行状况模型简介](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=Mooncake_0307_2016-->