---
title: 在 Linux 上为 Apache Tomcat 服务器创建 Azure Service Fabric 容器 | Microsoft Docs
description: 创建 Linux 容器，以在 Azure Service Fabric 上公开 Apache Tomcat 服务器上运行的应用程序。 生成包含应用程序和 Apache Tomcat 服务器的 Docker 映像，将该映像推送到容器注册表，然后生成并部署 Service Fabric 容器应用程序。
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 29208bcbdbe6ad01d0e1ac7343bd921f3287260a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580050"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>在 Linux 上创建运行 Apache Tomcat 服务器的 Service Fabric 容器
Apache Tomcat 是 Java Servlet 和 Java Server 技术的常见开源实现。 本文介绍如何使用 Apache Tomcat 和简单的 Web 应用程序生成容器，然后将该容器部署到运行 Linux 的 Service Fabric 群集并连接到 Web 应用程序。  

若要了解有关 Apache Tomcat 的详细信息，请参阅 [Apache Tomcat 主页](http://tomcat.apache.org/)。 

## <a name="prerequisites"></a>先决条件
* 一台运行以下软件的开发计算机：
  * [Service Fabric SDK 和工具](service-fabric-get-started-linux.md)。
  * [适用于 Linux 的 Docker CE](https://docs.docker.com/engine/installation/#prior-releases)。 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure 容器注册表中的容器注册表。 可以使用 [Azure 门户](../container-registry/container-registry-get-started-portal.md)或 [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry) 在 Azure 订阅中创建容器注册表。 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>生成 Tomcat 映像并在本地运行
按照本部分中的步骤，基于 Apache Tomcat 映像和简单的 Web 应用生成 Docker 映像，然后在本地系统的容器中运行该映像。 
 
1. 克隆开发计算机上的[通过 Java 开始使用 Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started) 示例存储库。

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. 将目录更改为 Apache Tomcat 服务器示例目录 (service-fabric-java-getting-started/container-apache-tomcat-web-server-sample)：

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. 根据位于 Docker 中心的正式 [Tomcat 映像](https://hub.docker.com/_/tomcat/)和 Tomcat 服务器示例创建 Docker 文件。 在 service-fabric-java-getting-started/container-apache-tomcat-web-server-sample 目录中，创建名为 Dockerfile 的文件（无文件扩展名）。 将以下内容添加到 *Dockerfile* 并保存所做的更改：

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   有关详细信息，请参阅 [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)（Dockerfile 参考）。


4. 运行 `docker build` 命令，创建运行上述 Web 应用程序的映像：

   ```bash
   docker build . -t tomcattest
   ```

   该命令按 Dockerfile 中的说明生成新映像，并将映像命名为（-t 表示标记）`tomcattest`。 若要生成容器映像，首先从 Docker 中心下载基础映像并在其上添加应用程序。 

   生成命令执行完以后，请运行 `docker images` 命令，查看有关新映像的信息：

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. 先验证容器化应用程序是否在本地运行，然后将它推送到容器注册表：
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` 命名容器，因此使用友好名称而不是 ID 进行引用。
   * `-p` 指定容器和主机 OS 之间的端口映射。 

   > [!Note]
   > 使用 `-p` 参数打开的端口应该是 Tomcat 应用程序侦听其请求的端口。 当前示例中，ApacheTomcat/conf/server.xml 文件中配置了连接器，用于侦听端口 8080 的 HTTP 请求。 此端口将映射到主机上的端口 8080。 

   若要了解其他参数，请参阅 [Docker 运行文档](https://docs.docker.com/engine/reference/commandline/run/)。

1. 若要测试容器，请打开浏览器并输入以下任一 URL。 将看到“Hello World!”的变体 每个 URL 的欢迎屏幕。

   - http://localhost:8080/hello 
   - http://localhost:8080/hello/sayhello 
   - http://localhost:8080/hello/sayhi 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. 停止容器，并将其从开发计算机中删除：

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>将 Tomcat 映像推送到容器注册表
现在，已确认 Tomcat 映像在在开发计算机上的容器中运行，将其推送到容器注册表中的存储库中。 本文使用 Azure 容器注册表来存储图像，但是，只需对步骤稍作修改即可使用所选的任何容器注册表。 本文中的注册表名称假定为 myregistry，完整注册表名称为 myregistry.azurecr.io。 可根据自己的方案相应更改上述内容。 

1. 运行 `docker login`，使用[注册表凭据](../container-registry/container-registry-authentication.md)登录到容器注册表。

   以下示例传递了 Azure Active Directory [服务主体](../active-directory/develop/app-objects-and-service-principals.md)的 ID 和密码。 例如，你可能在自动化方案中向注册表分配了服务主体。 或者，可以使用注册表用户名和密码登录。

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. 以下命令使用注册表的完全限定路径创建映像的标记或别名。 此示例将映像置于 `samples` 命名空间，以免注册表根目录中出现混乱。

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. 将映像推送到容器注册表：

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>生成和部署 Service Fabric 容器应用程序
现在，已将 Tomcat 映像推送到容器注册表，可以生成和部署从注册表中拉取 Tomcat 映像的 Service Fabric 容器应用程序并将其作为群集中的容器化服务运行。 

1. 在本地克隆外创建新的目录（在 service-fabric-java-getting-started 目录树外）。 切换到新目录，并使用 Yeoman 创建容器应用程序的基架： 

   ```bash
   yo azuresfcontainer 
   ```
   系统提示时输入以下值：

   * 命名应用程序：ServiceFabricTomcat
   * 应用程序服务名称：TomcatService
   * 输入映像名称：提供容器注册表中容器映像的 URL（例如，myregistry.azurecr.io/samples/tomcattest）。
   * 命令：将此选项留空。 此映像中定义了一个工作负荷入口点，因此不需显式指定输入命令（命令在容器中运行，这可以在启动后使容器保持运行）。
   * 来宾容器应用程序的实例数：1

   ![适用于容器的 Service Fabric Yeoman 生成器](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. 在服务清单 (ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml) 中的根 ServiceManfest 标记下添加以下 XML，打开应用程序侦听其请求的端口。 Endpoint 标记声明终结点的协议和端口。 本文所述的容器化服务侦听端口 8080： 

    ```xml
    <Resources>
      <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to 
         listen. Please note that if your service is partitioned, this port is shared with 
         replicas of different partitions that are placed in your code. -->
        <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
      </Endpoints>
    </Resources>
    ```

11. 在应用程序清单 (ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml) 中的 ServiceManifestImport 标记下，添加以下 XML。 将 RepositoryCredentials 标记中的 AccountName 和 Password 替换为所需的容器注册表名称和密码，以便登录。

    ```xml
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
      </ContainerHostPolicies>
    </Policies>
    ```

    ContainerHostPolicies 标记指定用于激活容器主机的策略。
    
    * PortBinding 标记配置容器端口到主机端口映射策略。 将 ContainerPort 属性设置为 8080，因为容器将公开端口 8080，如 Dockerfile 中所述。 将 EndpointRef 属性设置为上一步骤中的服务清单中定义的终结点“endpointTest”。 因此，传入到端口 8080 上的服务的请求将映射到容器上的端口 8080。 
    * RepositoryCredentials 标记指定容器从中拉取映像的（私有）存储库进行身份验证所需的凭据。 如果将从公共存储库拉取映像，则不需要此策略。
    

12. 在 ServiceFabricTomcat 文件夹中，请连接到 Service Fabric 群集。 

    * 若要连接到本地 Service Fabric 群集，请运行：

       ```bash
       sfctl cluster select --endpoint http://localhost:19080
       ```
    
    * 若要连接到安全的 Azure 群集，请确保客户端证书以 .pem 文件的形式存在于 ServiceFabricTomcat 目录中，并运行： 

       ```bash
       sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
       ```
       在上述命令中，将 `your-certificate.pem` 替换为客户端证书文件的名称。 在开发和测试环境中，群集证书通常用作客户端证书。 如果你的证书未自签名，则忽略 `-no-verify` 参数。 
       
       群集证书通常本地下载为 .pfx 文件。 如果你还没有 PEM 格式的证书，可以运行以下命令，通过 .pfx 文件创建 .pem 文件：

       ```bash
       openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
       ```

       如果 .pfx 文件不受密码保护，请对最后一个参数使用 `-passin pass:`。


13. 运行模板中提供的安装脚本，将应用程序部署到群集中。 该脚本将应用程序包复制到群集映像存储区，注册应用程序类型并创建应用程序的实例。

       ```bash
       ./install.sh
       ```

    运行安装脚本后，打开浏览器并导航到 Service Fabric Explorer：
    
    * 在本地群集上，请使用 http://localhost:19080/Explorer（如果在 Mac OS X 上使用 Vagran，将 localhost 替换为 VM 的私有 IP）。
    * 在安全的 Azure 群集上，请使用 https://PublicIPorFQDN:19080/Explorer。 
    
    展开应用程序节点，注意现在有一个条目是用于应用程序类型 (ServiceFabricTomcatType)，另一个条目用于该类型的第一个实例。 可能需要几分钟时间才能完全部署应用程序，因此请耐心等待。

    ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. 若要访问 Tomcat 服务器上的应用程序，请打开浏览器窗口并输入以下任一 URL。 如果部署到本地群集，则对 PublicIPorFQDN 使用 localhost。 将看到“Hello World!”的变体 每个 URL 的欢迎屏幕。

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>清理
使用模板中提供的卸载脚本从群集中删除应用程序实例并注销应用程序类型。

```bash
./uninstall.sh
```

将映像推送到容器注册表以后，即可从开发计算机中删除本地映像：

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>后续步骤
* 有关其他 Linux 容器功能的快速步骤，请参阅[在 Linux 上创建第一个 Service Fabric 容器应用程序](service-fabric-get-started-containers-linux.md)。
* 有关 Linux 容器的详细步骤，请参阅[创建 Linux 容器应用教程](service-fabric-tutorial-create-container-images.md)。
* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-containers-overview.md)。


