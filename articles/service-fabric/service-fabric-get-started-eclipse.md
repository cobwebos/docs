---
title: 适用于 Eclipse 的 Azure Service Fabric 插件 | Microsoft Docs
description: 适用于 Eclipse 的 Service Fabric 插件入门。
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: a9888a23088949b5373aa0eef7d4df3b3064466f
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358579"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>使用适用于 Eclipse 的 Service Fabric 插件开发 Java 应用程序
Eclipse 是面向 Java 开发人员的最常用集成开发环境 (IDE) 之一。 本文介绍如何设置适用于 Azure Service Fabric 的 Eclipse 开发环境。 了解如何安装 Service Fabric 插件、创建 Service Fabric 应用程序，以及将 Service Fabric 应用程序部署到 Eclipse 中的本地或远程 Service Fabric 群集。 

> [!NOTE]
> Windows 当前不支持 Eclipse 插件。 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>在 Eclipse 中安装或更新 Service Fabric 插件
可在 Eclipse 中安装 Service Fabric 插件。 该插件可帮助简化生成和部署 Java 服务的过程。

> [!IMPORTANT]
> Service Fabric 插件需要 Eclipse Neon 或更高版本。 请参阅此注意事项后面的说明，了解如何查看 Eclipse 的版本。 如果安装的是较旧版本的 Eclipse，可以从 [Eclipse 站点](https://www.eclipse.org)下载较新的版本。 建议不要在现有 Eclipse 安装版本的基础上进行安装（覆盖）。 可以先删除现有版本，然后再运行安装程序，也可以在另一目录中安装较新的版本。 
> 
> 在 Ubuntu 上，建议直接从 Eclipse 站点进行安装，而不是使用包安装程序（`apt` 或 `apt-get`）。 这样做可确保获取最新版 Eclipse。 

从 [Eclipse 站点](https://www.eclipse.org)安装 Eclipse Neon 或更高版本。  还安装 Buildship 2.2.1 版或更高版本（Service Fabric 插件与更旧版本的 Buildship 不兼容）：
-   若要检查已安装组件的版本，请在 Eclipse 中转到“帮助” > “关于 Eclipse” > “安装详细信息”。
-   若要更新 Buildship，请参阅 [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update]（Eclipse Buildship：适用于 Gradle 的 Eclipse 插件）。
-   若要检查并安装 Eclipse 的更新，请转到“帮助” > “检查更新”。

若要安装 Service Fabric 插件，请在 Eclipse 中转到“帮助” > “安装新软件”。
1. 在“使用”框中，输入 **http://dl.microsoft.com/eclipse**。
2. 单击 **“添加”**。

   ![适用于 Eclipse 的 Service Fabric 插件][sf-eclipse-plugin-install]
3. 选择 Service Fabric 插件，并单击“下一步”。
4. 完成安装步骤，并接受 Microsoft 软件许可条款。
  
如果已安装 Service Fabric 插件，请安装最新版本。 
1. 若要检查可用的更新，请转到“帮助” > “关于 Eclipse” > “安装详细信息”。 
2. 在已安装的插件列表中选择“Service Fabric”，并单击“更新”。 随后将安装可用的更新。
3. 更新 Service Fabric 插件后，还需刷新 Gradle 项目。  右键单击 **build.gradle**，然后选择“刷新”。

> [!NOTE]
> 如果安装或更新 Service Fabric 插件时运行缓慢，原因可能是 Eclipse 设置有问题。 Eclipse 将收集有关所有更改的元数据，以更新已注册到 Eclipse 实例的站点。 若要加速 Service Fabric 插件更新的检查和安装过程，请转到“可用软件站点”。 清除所有站点对应的复选框，但指向 Service Fabric 插件位置 (http://dl.microsoft.com/eclipse/azure/servicefabric) 的站点除外。

> [!NOTE]
>如果 Eclipse 在 Mac 上未按预期方式工作，或者需要你以超级用户身份运行，请转到 ECLIPSE_INSTALLATION_PATH 文件夹，然后导航到子文件夹 Eclipse.app/Contents/MacOS。 运行 `./eclipse` 启动 Eclipse。


## <a name="create-a-service-fabric-application-in-eclipse"></a>在 Eclipse 中创建 Service Fabric 应用程序

1.  在 Eclipse 中，转到“文件” > “新建” > “其他”。 选择“Service Fabric 项目”，并单击“下一步”。

    ![Service Fabric 新建项目第 1 页][create-application/p1]

2.  输入项目的名称，并单击“下一步”。

    ![Service Fabric 新建项目第 2 页][create-application/p2]

3.  从模板列表中，选择“服务模板”。 选择服务模板的类型（“执行组件”、“无状态”、“容器”或“来宾二进制”），并单击“下一步”。

    ![Service Fabric 新建项目第 3 页][create-application/p3]

4.  输入服务名称和服务详细信息，并单击“完成”。

    ![Service Fabric 新建项目第 4 页][create-application/p4]

5. 创建第一个 Service Fabric 项目时，请在“打开关联的透视图”对话框中单击“是”。

    ![Service Fabric 新建项目第 5 页][create-application/p5]

6.  新项目如下所示：

    ![Service Fabric 新建项目第 6 页][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>在 Eclipse 中生成 Service Fabric 应用程序

1.  右键单击新建的 Service Fabric 应用程序，并选择“Service Fabric”。

    ![Service Fabric 右键菜单][publish/RightClick]

2. 在上下文菜单中，选择以下选项之一：
    -   若要生成应用程序但不清理，请单击“生成应用程序”。
    -   若要生成已清理的应用程序，请单击“重新生成应用程序”。
    -   若要清理已生成项目的应用程序，请单击“清理应用程序”。
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>在 Eclipse 中将 Service Fabric 应用程序部署到本地群集

生成 Service Fabric 应用程序后，请遵循以下步骤将其部署到本地群集。

1. 如果尚未启动本地群集，请遵照[设置本地群集](./service-fabric-get-started-linux.md#set-up-a-local-cluster)中的说明启动本地群集，并确保该群集正在运行。
2. 右键单击 Service Fabric 应用程序，并选择“Service Fabric”。

    ![Service Fabric 右键菜单][publish/RightClick]

3.  在上下文菜单中，单击“部署应用程序”。
4.  可以在控制台窗口中跟踪部署操作的进度。
5.  若要验证应用程序是否正在运行，请在浏览器窗口中的本地群集上打开 Service Fabric Explorer ([http://localhost:19080/Explorer](http://localhost:19080/Explorer))。 展开“应用程序”节点，确保应用程序正在运行。 

若要了解如何使用本地群集在 Eclipse 中调试应用程序，请参阅[在 Eclipse 中调试 Java 服务](./service-fabric-debugging-your-application-java.md)。

也可以使用“发布应用程序”命令将应用程序部署到本地群集：

1. 右键单击 Service Fabric 应用程序，并选择“Service Fabric”。
2. 在上下文菜单中，单击“发布应用程序...”。
3. 在“发布应用程序”窗口中，选择“PublishProfiles/Local.json”为目标配置文件，然后单击“发布”。

    ![本地“发布”对话框](./media/service-fabric-get-started-eclipse/localjson.png)

    默认情况下，Local.json 发布配置文件设置为发布到本地群集。 有关发布配置文件中提供的连接和终结点参数的详细信息，请参阅下一部分。

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>在 Eclipse 中将 Service Fabric 应用程序发布到 Azure

若要将应用程序发布到云中，请执行以下步骤：

1. 若要将应用程序发布到云中的安全群集，需要使用 X.509 证书来与群集通信。 在测试和开发环境中，使用的证书通常是群集证书。 在生产环境中，该证书应该是不同于群集证书的客户端证书。 需要证书和私钥。 证书（和密钥）文件必须采用 PEM 格式。 可以使用以下 openssl 命令，从 PFX 文件创建包含证书和私钥的 PEM 文件：

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   如果 PFX 文件不受密码保护，请对最后一个参数使用 `--passin pass:`。

2. 打开 **PublishProfiles** 目录下的 **Cloud.json** 文件。 需要适当地为群集配置群集终结点和安全凭据。

   - `ConnectionIPOrURL` 字段包含群集的 IP 地址或 URL。 请注意，值不包含 URL 方案 (`https://`)。
   - `ConnectionPort` 字段默认为 `19080`，除非为群集显式更改了此端口。
   - `ClientKey` 字段应指向本地计算机上包含客户端或群集证书私钥的 PEM 格式的 .pem 或 .key 文件。
   - `ClientCert` 字段应指向本地计算机上包含客户端或群集证书数据的 PEM 格式的 .pem 或 .crt 文件。 证书。 

    ```bash
    {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. 右键单击 Service Fabric 应用程序，并选择“Service Fabric”。
3. 在上下文菜单中，单击“发布应用程序...”。
3. 在“发布应用程序”窗口中，选择“PublishProfiles/Cloud.json”为目标配置文件，然后单击“发布”。

    ![云端“发布”对话框](./media/service-fabric-get-started-eclipse/cloudjson.png)

4.  可以在控制台窗口中跟踪发布操作的进度。
5.  若要验证应用程序是否正在运行，请在浏览器窗口中的 Azure 群集上打开 Service Fabric Explorer。 在上述示例中，URL 为：`https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`。 展开“应用程序”节点，确保应用程序正在运行。 


在安全 Linux 群集上，如果应用程序包含 Reliable Services 服务，则还需要配置可供服务用来调用 Service Fabric 运行时 API 的证书。 若要了解详细信息，请参阅[将 Reliable Services 应用程序配置为在 Linux 群集上运行](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)。

有关如何将以 Java 编写的 Service Fabric Reliable Services 应用程序部署到安全 Linux 群集的快速演练，请参阅[快速入门：部署 Java Reliable Services 应用程序](./service-fabric-quickstart-java-reliable-services.md)。

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>使用 Eclipse 运行配置部署 Service Fabric 应用程序

部署 Service Fabric 应用程序的另一种方式是使用 Eclipse 运行配置。

1. 在 Eclipse 中，转到“运行” > “运行配置”。
2. 在“Gradle 项目”下面，选择“ServiceFabricDeployer”运行配置。
3. 在右窗格中的“参数”选项卡上，确保适当地为部署设置了 **ip**、**port**、**clientCert** 和 **clientKey** 参数。 这些参数默认设置为部署到本地群集，如以下屏幕截图中所示。 若要将应用发布到 Azure，可以修改参数，以包含 Azure 群集的终结点详细信息和安全凭据。 有关详细信息，请参阅上一部分[在 Eclipse 中将 Service Fabric 应用程序发布到 Azure](#publish-your-service-fabric-application-to-azure-with-eclipse)。

    ![运行配置对话框 - 本地](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. 确保“工作目录”指向要部署的应用程序。 要更改应用程序，请单击“工作区”按钮，并选择所需的应用程序。
6. 单击“应用”，并单击“运行”。

应用程序会在片刻之后生成和部署。 可在 Service Fabric Explorer 中监视部署状态。  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>将 Service Fabric 服务添加到 Service Fabric 应用程序

要将 Service Fabric 服务添加到现有的 Service Fabric 应用程序，请执行以下步骤：

1.  右键单击要将该服务添加到的项目，然后单击“Service Fabric”。

    ![Service Fabric 添加服务第 1 页][add-service/p1]

2.  单击“添加 Service Fabric 服务”，完成将服务添加到项目的一系列步骤。
3.  选择要添加到项目的服务模板，并单击“下一步”。

    ![Service Fabric 添加服务第 2 页][add-service/p2]

4.  输入服务名称（根据需要输入其他详细信息），并单击“添加服务”按钮。  

    ![Service Fabric 添加服务第 3 页][add-service/p3]

5.  添加该服务后，整个项目结构类似于以下项目：

    ![Service Fabric 添加服务第 4 页][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>编辑 Service Fabric Java 应用程序的清单版本

要编辑清单版本，请右键单击项目，转到“Service Fabric”，并从下拉菜单中选择“编辑清单版本...”。 在向导中，可以更新应用程序清单和服务清单的清单版本，以及“编码”、“配置”和“数据”包的版本。

如果选择“自动更新应用程序和服务的版本”选项，并更新了一个版本，则会自动更新清单版本。 举例来说，首先选择了该复选框，然后将“代码”版本从 0.0.0 更新为 0.0.1，最后再单击“完成”，然后服务清单版本和应用程序清单版本就会自动更新为 0.0.1。

## <a name="upgrade-your-service-fabric-java-application"></a>升级 Service Fabric Java 应用程序

假设你在升级方案中使用 Service Fabric 插件在 Eclipse 中创建了 **App1** 项目。 已使用该插件部署了该项目，以创建名为 **fabric:/App1Application** 的应用程序。 该应用程序的类型为 **App1AppicationType**，应用程序版本为 1.0。 现在，你想要在不影响可用性的情况下升级该应用程序。

首先，请对应用程序进行任何更改，然后重新生成已修改的服务。 使用服务的更新版本（以及相关的代码、配置或数据）更新已修改服务的清单文件 (ServiceManifest.xml)。 此外，使用应用程序和已修改服务的更新版本号修改应用程序的清单 (ApplicationManifest.xml)。  

若要使用 Eclipse 升级应用程序，可以创建重复的运行配置文件。 然后，根据需要使用该文件升级应用程序。

1.  转到“运行” > “运行配置”。 在左窗格中，单击“Gradle 项目”左侧的小箭头。
2.  右键单击“ServiceFabricDeployer”，并选择“复制”。 输入此配置的新名称，例如 **ServiceFabricUpgrader**。
3.  在右侧面板中的“参数”选项卡上，将 **-Pconfig='deploy'** 更改为 **-Pconfig='upgrade'**，然后单击“应用”。

此过程将创建并保存随时可用于升级应用程序的运行配置配置文件。 此过程还会从应用程序清单文件中获取最近更新的应用程序类型版本。

应用程序升级需要几分钟时间。 可在 Service Fabric Explorer 中监视应用程序升级状态。

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>迁移要与 Maven 配合使用的旧式 Service Fabric Java 应用程序
我们最近已将 Service Fabric Java 库从 Service Fabric Java SDK 移至 Maven 存储库。 虽然使用 Eclipse 生成的新应用程序会生成最近更新的项目（会兼容 Maven），但你可以对现有的 Service Fabric 无状态或执行组件 Java 应用程序（此前使用 Service Fabric Java SDK）进行更新，使之能够使用 Maven 提供的 Service Fabric Java 依赖项。 请按[此处](service-fabric-migrate-old-javaapp-to-use-maven.md)提及的步骤操作，确保旧版应用程序兼容 Maven。

## <a name="next-steps"></a>后续步骤

- 有关生成 Java Reliable Services 应用程序并将其部署到本地和 Azure 的快速步骤，请参阅[快速入门：部署 Java Reliable Services 应用程序](./service-fabric-quickstart-java-reliable-services.md)。
- 若要了解如何在本地群集上调试 Java 应用程序，请参阅[在 Eclipse 中调试 Java 服务](./service-fabric-debugging-your-application-java.md)。
- 若要了解如何监视和诊断 Service Fabric 应用程序，请参阅[在本地计算机开发设置中监视和诊断服务](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)。

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
