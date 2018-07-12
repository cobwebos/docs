---
title: 在 Azure 中的 Service Fabric 上创建 Spring Boot 应用 | Microsoft Docs
description: 在本快速入门中，请使用 Spring Boot 示例应用程序为 Azure Service Fabric 部署 Spring Boot 应用程序。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 83cd90babaa5bcb396f792c7e933d38b3911cebb
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970350"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-service-fabric"></a>快速入门：将 Java Spring Boot 应用程序部署到 Service Fabric

Azure Service Fabric 是一款分布式系统平台，可用于部署和管理微服务和容器。

本快速入门演示如何将 Spring Boot 应用程序部署到 Service Fabric。 本快速入门使用 Spring 网站中的[入门](https://spring.io/guides/gs/spring-boot/)示例。 本快速入门逐步讲解如何使用熟悉的命令行工具，将 Spring Boot 示例部署为 Service Fabric 应用程序。 完成后，Spring Boot 入门示例将在 Service Fabric 上正常运行。

![应用程序屏幕截图](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

此快速入门介绍如何：

* 将 Spring Boot 应用程序部署到 Service Fabric
* 将应用程序部署到本地群集
* 将应用程序部署到 Azure 中的群集
* 跨多个节点横向扩展应用程序
* 在不影响可用性的情况下执行服务故障转移

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

1. 安装 Service Fabric SDK 和 Service Fabric 命令行界面 (CLI)

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

2. [安装 Git](https://git-scm.com/)
3. 安装 Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. 设置 Java 环境

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>下载示例

在终端窗口中运行以下命令，将 Spring Boot 入门示例应用克隆到本地计算机。

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>生成 Spring Boot 应用程序 
1. 在 `gs-spring-boot/complete` 目录中，运行以下命令以生成此应用程序。 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>打包 Spring Boot 应用程序 
1. 在克隆的 `gs-spring-boot` 目录中运行 `yo azuresfguest` 命令。 

2. 每次出现提示时，请输入以下详细信息。

    ![Yeoman 输入内容](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. 在 `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` 文件夹中，创建名为 `entryPoint.sh` 的文件。 将以下内容添加到 `entryPoint.sh` 文件中。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

4. 在 `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml` 文件中添加**终结点**资源

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    **ServiceManifest.xml** 现在如下所示： 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

现已创建 Spring Boot 入门示例的 Service Fabric 应用程序，可将它部署到 Service Fabric。

## <a name="run-the-application-locally"></a>在本地运行应用程序

1. 通过运行以下命令来启动 Ubuntu 计算机上的本地群集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    如果使用 Mac，请从 Docker 映像启动本地群集（此处假定你已按照[先决条件](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric)设置适用于 Mac 的本地群集）。 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    启动本地群集需要一些时间。 若要确认群集是否完全正常，请访问 Service Fabric Explorer（网址：**http://localhost:19080**）。 5 个节点均正常即表示本地群集运行正常。 
    
    ![本地群集正常运行](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. 导航到 `gs-spring-boot/SpringServiceFabric` 文件夹。
3. 运行以下命令连接到本地群集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. 运行 `install.sh` 脚本。

    ```bash
    ./install.sh
    ```

5. 打开喜欢的 Web 浏览器并访问应用程序（网址：**http://localhost:8080**）。

    ![本地应用程序前端](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

现在可以访问已部署到 Service Fabric 群集的 Spring Boot 应用程序。

## <a name="deploy-the-application-to-azure"></a>将应用程序部署到 Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>设置 Azure Service Fabric 群集

若要将应用程序部署到 Azure 中的群集，可创建自己的群集。

合作群集是 Azure 上托管的免费限时 Service Fabric 群集，由 Service Fabric 团队运行。 可以通过合作群集来部署应用程序和了解平台。 该群集使用单个自签名证书来确保节点到节点和客户端到节点的安全。

登录并加入 [Linux 群集](http://aka.ms/tryservicefabric)。 通过单击 **PFX** 链接，将 PFX 证书下载到计算机。 单击**自述文件**链接，找到证书密码和说明，了解如何配置使用此证书所需的各种环境。 让“欢迎”页和“自述文件”页保持打开状态，然后需按照以下步骤中的某些说明进行操作。

> [!Note]
> 每小时可用的合作群集数目有限。 如果在尝试注册合作群集时出错，可以等待一段时间再重试，或者遵循[在 Azure 上创建 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)中的步骤，在订阅中创建一个群集。
>
> Spring Boot 服务配置为侦听端口 8080 上的传入流量。 请确保此端口在群集中处于打开状态。 如果使用的是合作群集，此端口已处于打开状态。
>

Service Fabric 提供多种可以用来管理群集及其应用程序的工具：

* Service Fabric Explorer，一种基于浏览器的工具。
* Service Fabric 命令行界面 (CLI)，在 Azure CLI 2.0 基础上运行。
* PowerShell 命令。

在本快速入门中，请使用 Service Fabric CLI 和 Service Fabric Explorer。

若要使用 CLI，需根据下载的 PFX 文件创建 PEM 文件。 若要转换此文件，请使用以下命令。 （对于合作群集，可以从“自述文件”页上的说明中复制特定于 PFX 文件的命令。）

```bash
openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
``` 

若要使用 Service Fabric Explorer，需将从合作群集网站下载的证书 PFX 文件导入到证书存储（Windows 或 Mac）中，或者导入到浏览器 (Ubuntu) 中。 需要可以从“自述文件”页获取的 PFX 私钥密码。

请使用最熟悉的方法将证书导入到系统中。 例如：

* 在 Windows 上：双击 PFX 文件，按提示在个人存储 `Certificates - Current User\Personal\Certificates` 中安装证书。 也可以使用**自述文件**说明中的 PowerShell 命令。
* 在 Mac 上：双击 PFX 文件，按提示在 Keychain 中安装证书。
* 在 Ubuntu 上：Mozilla Firefox 是 Ubuntu 16.04 中的默认浏览器。 若要将证书导入 Firefox，请单击浏览器右上角的菜单按钮，然后单击“选项”。 在“首选项”页上，使用搜索框搜索“证书”。 单击“查看证书”，选择“你的证书”选项卡，单击“导入”，然后按提示导入证书。

   ![在 Firefox 上安装证书](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png)

### <a name="deploy-the-application-using-cli"></a>使用 CLI 部署应用程序

应用程序和群集准备就绪后，可通过命令行将应用程序直接部署到群集。

1. 导航到 `gs-spring-boot/SpringServiceFabric` 文件夹。
2. 运行以下命令连接到 Azure 群集。

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. 运行 `install.sh` 脚本。

    ```bash
    ./install.sh
    ```

4. 打开 Web 浏览器并通过 **http://\<ConnectionIPOrUrl>:8080** 访问该应用程序。

    ![本地应用程序前端](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)

现在可以访问在 Azure 上的 Service Fabric 群集中运行的 Spring Boot 应用程序。

## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务

可跨群集缩放服务来适应服务负载的变化。 可以通过更改群集中运行的实例数量来缩放服务。 存在多种服务缩放方式，例如，可使用 Service Fabric CLI (sfctl) 脚本/命令。 以下步骤使用 Service Fabric Explorer。

Service Fabric Explorer 在所有 Service Fabric 群集中运行，并能通过浏览器进行访问，访问方法是转到群集的 HTTP 管理端口 (19080)，例如，`http://localhost:19080`。

若要缩放 Web 前端服务，请执行以下操作：

1. 在群集中打开 Service Fabric Explorer - 例如 `http://localhost:19080`。
2. 在树视图中单击“fabric:/SpringServiceFabric/SpringGettingStarted”节点旁边的省略号（三个点），选择“缩放服务”。

    ![Service Fabric Explorer 缩放服务](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    现在可以选择缩放服务的实例数量。

3. 将数字更改为 **3**，单击“缩放服务”。

    下面显示了使用命令行缩放服务的另一种方法。

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

4. 在树视图中单击“fabric:/SpringServiceFabric/SpringGettingStarted”节点，展开分区节点（由 GUID 表示）。

    ![Service Fabric Explorer 缩放服务完成](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    此服务有三个实例。树状视图显示实例在哪些节点上运行。

通过这一简单的管理任务，你已让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需多个实例便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="fail-over-services-in-a-cluster"></a>故障转移群集中的服务

为了演示服务故障转移，可以使用 Service Fabric Explorer 来模拟节点重启。 请确保只有一个服务实例在运行。

1. 在群集中打开 Service Fabric Explorer - 例如 `http://localhost:19080`。
2. 单击运行服务实例的节点旁边的省略号（三个点），并重启节点。

    ![Service Fabric Explorer - 重启节点](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. 服务实例已转移到另一个节点，且应用程序并未关闭。

    ![Service Fabric Explorer - 重启节点成功](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 将 Spring Boot 应用程序部署到 Service Fabric
* 将应用程序部署到本地群集
* 将应用程序部署到 Azure 中的群集
* 跨多个节点横向扩展应用程序
* 在不影响可用性的情况下执行服务故障转移

若要详细了解如何在 Service Fabric 中使用 Java 应用，请继续学习适用于 Java 应用的教程。

> [!div class="nextstepaction"]
> [部署 Java 应用](./service-fabric-tutorial-create-java-app.md)