---
title: 生产就绪情况和最佳实践-Azure
description: 本文提供有关如何在生产环境中的 IoT Edge 模块上配置和部署实时视频分析的指导。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a5a2ff78d456d4423facdf5f3533ee94bc25bfc4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260370"
---
# <a name="production-readiness-and-best-practices"></a>生产就绪情况和最佳做法

本文提供有关如何在生产环境中的 IoT Edge 模块上配置和部署实时视频分析的指导。 还应查看在[生产中部署 IoT Edge 解决方案的准备](https://docs.microsoft.com/azure/iot-edge/production-checklist)工作一文，了解如何准备 IoT Edge 解决方案。 

> [!NOTE]
> 你应向组织的 IT 部门咨询与安全性相关的方面。

## <a name="running-the-module-as-a-local-user"></a>以本地用户的身份运行模块

在将 IoT Edge 模块上的实时视频分析部署到边缘设备时，默认情况下，它将使用提升的权限运行。 当你执行此操作时，如果你检查模块上的日志（ `sudo iotedge logs {name-of-module}` ），你将看到以下内容：

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

以下部分介绍如何解决上述警告。

### <a name="creating-and-using-a-local-user-account"></a>创建和使用本地用户帐户

你可以使用一个具有尽可能少权限的帐户在生产中 IoT Edge 模块上运行实时视频分析。 例如，以下命令显示了如何在 Linux VM 上创建本地用户帐户：

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

接下来，在部署清单中，可以将 LOCAL_USER_ID 和 LOCAL_GROUP_ID 环境变量设置为该非根用户和组：

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>授予设备存储的权限

IoT Edge 模块上的实时视频分析需要在以下情况时能够将文件写入本地文件系统：

* 使用模块克隆属性 [[applicationDataDirectory](module-twin-configuration-schema.md#module-twin-properties)]，你应该在其中指定本地文件系统上用于存储配置数据的目录。
* 使用 media graph 将视频录制到云，此模块需要使用边缘设备上的目录作为缓存（有关详细信息，请参阅[连续视频录制](continuous-video-recording-concept.md)一文）。
* [记录到本地文件](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)，您应该在其中指定所录制视频的文件路径。

如果你打算使用上述任何一个，则应确保上述用户帐户有权访问相关目录。 例如，applicationDataDirectory。 可以在边缘设备上创建一个目录，并将设备存储链接到模块存储。 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

接下来，在部署清单中的 edge 模块的 "创建选项" 中，可以将上面的目录（"var/local/windowsazure.mediaservices/"）映射到模块中的目录（如 "/var/lib/azuremediaservices/"）添加绑定设置。 使用后一目录作为 applicationDataDirectory 的值。

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

如果你查看快速入门和教程的示例媒体图形，如[连续视频录制](continuous-video-recording-tutorial.md)，你会注意到，媒体缓存目录（localMediaCachePath）使用 applicationDataDirectory 下的子目录。 这是建议的方法，因为缓存包含暂时性数据。

### <a name="naming-video-assets-or-files"></a>命名视频资产或文件

Media graph 允许在云中创建资产或在边缘上创建有文件的文件。 媒体资产可以通过[连续视频录制](continuous-video-recording-tutorial.md)或[基于事件的视频录制](event-based-video-recording-tutorial.md)生成。 尽管这些资产和文件可以按您的需要命名，但建议用于连续视频录制的媒体资产的命名结构是 " &lt; anytext &gt; -$ {GraphTopologyName}-$ {GraphInstanceName}"。 例如，可以在资产接收器上设置 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

对于基于事件的视频记录生成的资产，推荐的命名模式为 " &lt; anytext &gt; -$ {system.object}"。 系统变量可确保在事件同时发生时不会覆盖资产。 例如，可以在资产接收器上设置 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

如果正在运行同一关系图的多个实例，则可以使用关系图拓扑名称和实例名称来区分。 例如，可以在资产接收器上设置 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName} -${System.DateTime}"
```

对于基于事件的视频记录-在边缘上生成的未生成的视频剪辑，推荐的命名模式应该包含日期时间和相同图形的多个实例，建议使用系统变量 GraphTopologyName 和 GraphInstanceName。 例如，可以在文件接收器上设置 filePathPattern，如下所示： 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

或 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>保持 VM 整洁

如果要用作边缘设备的 Linux VM 未定期管理，则它可能会变得不响应。 务必将缓存保持干净，消除不必要的包，并从 VM 中删除未使用的容器。 若要执行此操作，可以在边缘 VM 上使用一组建议的命令。

1. `sudo apt-get clean`

    Apt-clean 命令可清除已检索包文件的本地存储库，这些文件保留在/var/cache. 中。 它清除的目录是/var/cache/apt/archives/和/var/cache/apt/archives/partial/。 它保留在/var/cache/apt/archives 中的唯一文件是锁定文件和部分子目录。 Apt-get clean 命令通常用于根据需要清除磁盘空间，通常作为定期维护维护的一部分。 有关详细信息，请参阅[apt-get 的清理](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)。
1. `sudo apt-get autoclean`

    Apt-get autoclean 选项（如 apt）会清除检索到的包文件的本地存储库，但它只会删除无法再下载并且不会有用处的文件。 这有助于防止缓存的增长过大。
1. `sudo apt-get autoremove1`

    "自动删除" 选项会删除自动安装的包，因为其他某个包需要其他包，但在删除这些包后，不再需要这些包
1. `sudo docker image ls`–提供边缘系统上的 Docker 映像列表
1. `sudo docker system prune `

    Docker 采用保守的方法来清理未使用的对象（通常称为 "垃圾回收"），如图像、容器、卷和网络：这些对象通常不会被删除，除非明确要求 Docker 这样做。 这可能会导致 Docker 占用额外的磁盘空间。 对于每种类型的对象，Docker 提供了一个修剪命令。 此外，还可以使用 docker 系统修剪同时清理多个类型的对象。 有关详细信息，请参阅[修剪未使用的 Docker 对象](https://docs.docker.com/config/pruning/)。
1. `sudo docker rmi REPOSITORY:TAG`

    当边缘模块上发生更新时，docker 可以具有较旧版本的边缘模块。 在这种情况下，建议使用 docker rmi 命令删除映像版本标记标识的特定映像。

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md)
