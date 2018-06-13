---
title: 使用 Azure Kubernetes 服务 (AKS) 运行 Apache Spark 作业
description: 使用 Azure Kubernetes 服务 (AKS) 运行 Apache Spark 作业
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: cb23c21fd22a35a3e8a5920a94aa5a89fe966cfa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934939"
---
# <a name="running-apache-spark-jobs-on-aks"></a>在 AKS 中运行 Apache Spark 作业

[Apache Spark][apache-spark] 是用于大规模数据处理的高速引擎。 从 [Spark 2.3.0 版][spark-latest-release]开始，Apache Spark 原生支持与 Kubernetes 群集集成。 Azure Kubernetes 服务 (AKS) 是 Azure 中运行的托管 Kubernetes 环境。 本文档详细说明如何在 Azure Kubernetes 服务 (AKS) 群集上准备和运行 Apache Spark 作业。

## <a name="prerequisites"></a>先决条件

为了完成本文中的步骤，需要具备以下各项。

* 基本了解 Kubernetes 和 [Apache Spark][spark-quickstart]。
* [Docker 中心][docker-hub]帐户，或 [Azure 容器注册表][acr-create]。
* 已在开发系统上[安装][azure-cli] Azure CLI。
* 已在系统上安装 [JDK 8][java-install]。
* 已在系统上安装 SBT（[Scala 生成工具][sbt-install]）。
* 已在系统上安装 Git 命令行工具。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

Spark 用于大规模数据处理，要求根据 Spark 资源的要求调整 Kubernetes 节点的大小。 我们建议对 Azure Kubernetes 服务 (AKS) 节点至少使用 `Standard_D3_v2` 大小。

如果需要一个可以满足此最低建议要求的 AKS 群集，请运行以下命令。

为群集创建资源组。

```azurecli
az group create --name mySparkCluster --location eastus
```

创建 AKS 群集，其中包含大小为 `Standard_D3_v2` 的节点。

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

连接到 AKS 群集。

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

如果使用 Azure 容器注册表 (ACR) 来存储容器映像，请在 AKS 与 ACR 之间配置身份验证。 请参阅 [ACR 身份验证文档][acr-aks]来了解相关步骤。

## <a name="build-the-spark-source"></a>生成 Spark 源

在 AKS 群集上运行 Spark 作业之前，需要生成 Spark 源代码并将其打包到容器映像中。 Spark 源包含可用于完成此过程的脚本。

将 Spark 项目存储库克隆到开发系统。

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

切换到克隆的存储库所在的目录，并将 Spark 源的路径保存到某个变量。

```bash
cd spark
sparkdir=$(pwd)
```

如果已安装多个 JDK 版本，请将 `JAVA_HOME` 设置为使用当前会话的版本 8。

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

运行以下命令，在 Kubernetes 的支持下生成 Spark 源代码。

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

以下命令创建 Spark 容器映像并将其推送到容器映像注册表。 将 `registry.example.com` 替换为容器注册表名称，将 `v1` 替换为要使用的标记。 如果使用 Docker 中心，则此值是注册表名称。 如果使用 Azure 容器注册表 (ACR)，则此值是 ACR 登录服务器名称。

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

将容器映像推送到容器映像注册表。

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>准备 Spark 作业

接下来，请准备 Spark 作业。 使用一个 jar 文件来保存 Spark 作业；运行 `spark-submit` 命令时，需要指定此文件。 可以通过公共 URL 访问该 jar 文件，或者将它预先打包在容器映像中。 在此示例中，已创建一个示例 jar 来计算 Pi 的值。 然后，此 jar 将上传到 Azure 存储。 如果有现有的 jar，请任意替换 jar 文件

创建一个目录，以便在其中创建 Spark 作业的项目。

```bash
mkdir myprojects
cd myprojects
```

通过模板创建新的 Scala 项目。

```bash
sbt new sbt/scala-seed.g8
```

出现提示时，请输入 `SparkPi` 作为项目名称。

```bash
name [Scala Seed Project]: SparkPi
```

导航到新建的项目目录。

```bash
cd sparkpi
```

运行以下命令来添加 SBT 插件，以便将项目打包为 jar 文件。

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

运行以下命令，将示例代码复制到新建的项目，并添加全部所需的依赖项。

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

若要将项目打包成 jar，请运行以下命令。

```bash
sbt assembly
```

成功打包后，应会看到如下所示的输出。

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>将作业复制到存储

创建 Azure 存储帐户和容器用于保存 jar 文件。

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

使用以下命令将 jar 文件上传到 Azure 存储帐户。

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

现在，变量 `jarUrl` 包含 jar 文件的可公开访问路径。

## <a name="submit-a-spark-job"></a>提交 Spark 作业

使用如下命令在单独的命令行中启动 kube 代理。

```bash
kubectl proxy
```

导航回到 Spark 存储库的根目录。

```bash
cd $sparkdir
```

使用 `spark-submit` 提交作业。

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

此操作会启动 Spark 作业，该作业将作业状态流式传输到 shell 会话。 运行作业时，可以使用 kubectl get pods 命令查看 Spark 驱动程序 pod 和执行器 pod。 打开另一个终端会话以运行这些命令。

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

运行作业时，还可以访问 Spark UI。 在第二个终端会话中，使用 `kubectl port-forward` 命令提供对 Spark UI 的访问权限。

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

若要访问 Spark UI，请在浏览器中打开地址 `127.0.0.1:4040`。

![Spark UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>获取作业结果和日志

作业完成后，驱动程序 pod 将处于“已完成”状态。 使用以下命令获取 pod 的名称。

```bash
kubectl get pods --show-all
```

输出：

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

使用 `kubectl logs` 命令从 spark 驱动程序 pod 获取日志。 将 pod 名称替换为驱动程序 pod 的名称。

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

在这些日志中，可以看到 Spark 作业的结果，即 Pi 的值。

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>使用容器映像打包 jar

在上述示例中，Spark jar 文件已上传到 Azure 存储。 另一种做法是将 jar 文件打包成自定义生成的 Docker 映像。

为此，请在 `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` 目录中查找 Spark 映像的 `dockerfile`。 在 `WORKDIR` 与 `ENTRYPOINT` 声明之间的某个位置为 Spark 作业 `jar` 添加 `ADD` 语句。

将 jar 路径更新为 `SparkPi-assembly-0.1.0-SNAPSHOT.jar` 文件在开发系统上的位置。 也可以使用自己的自定义 jar 文件。

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

使用随附的 Spark 脚本生成并推送映像。

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

运行作业时，可以不指示远程 jar URL，而是将 `local://` 方案与 Docker 映像中 jar 文件的路径结合使用。

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> 来自 Spark [文档][spark-docs]：“Kubernetes 计划程序当前处于实验阶段。 将来版本中可能在配置、容器映像和入口点方面有一些方行为更改。”

## <a name="next-steps"></a>后续步骤

查看 Spark 文档了解更多详细信息。

> [!div class="nextstepaction"]
> [Spark 文档][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
