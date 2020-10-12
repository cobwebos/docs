---
title: 生产就绪情况和最佳做法 - Azure
description: 本文提供如何在生产环境中配置和部署 IoT Edge 上的实时视频分析模块的指南。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c34e05e184cfa6f0933701a76177fae3eed70c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071942"
---
# <a name="production-readiness-and-best-practices"></a>生产就绪情况和最佳做法

本文提供如何在生产环境中配置和部署 IoT Edge 上的实时视频分析模块的指南。 在准备 IoT Edge 解决方案时，你还应该参阅[准备在生产环境中部署 IoT Edge 解决方案](../../iot-edge/production-checklist.md)一文。 

> [!NOTE]
> 你应向组织的 IT 部门咨询与安全性相关的方面。

## <a name="running-the-module-as-a-local-user"></a>以本地用户的身份运行模块

将 IoT Edge 上的实时视频分析模块部署到边缘设备时，默认情况下，它将以提升的权限运行。 执行此操作时，如果查看模块上的日志 (`sudo iotedge logs {name-of-module}`)，你将看到以下内容：

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

以下各部分讨论了如何处理上述警告。

### <a name="creating-and-using-a-local-user-account"></a>创建和使用本地用户帐户

你可以并且应该使用权限尽可能少的帐户在生产环境中运行 IoT Edge 上的实时视频分析模块。 例如，以下命令展示了如何在 Linux VM 上创建本地用户帐户：

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

### <a name="granting-permissions-to-device-storage"></a>授予设备存储权限

IoT Edge 上的实时视频分析模块需要能在执行以下操作时将文件写入本地文件系统：

* 使用模块孪生属性 [[applicationDataDirectory](module-twin-configuration-schema.md#module-twin-properties)] 时，你应通过此属性指定本地文件系统上用于存储配置数据的目录。
* 使用媒体图将视频录制到云中时，此模块需要将边缘设备上的目录用作缓存（有关详细信息，请参阅[连续视频录制](continuous-video-recording-concept.md)一文）。
* [录制到本地文件](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)，你应该为录制的视频指定文件路径。

如果你打算使用上述任何操作，应确保上述用户帐户有权访问相关目录。 例如 applicationDataDirectory。 你可以在边缘设备上创建目录，并将设备存储链接到模块存储。 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

接下来，在部署清单中的 edge 模块的 "创建选项" 中，可以添加一个绑定设置，将上述目录 ( "var/local/windowsazure.mediaservices/" ) 映射到模块 (如 "/var/lib/azuremediaservices/" ) 中的目录。 并且将后一个目录用作 applicationDataDirectory 的值。

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

如果查看快速入门和教程的示例媒体图（例如[连续视频录制](continuous-video-recording-tutorial.md)），你会注意到媒体缓存目录 (localMediaCachePath) 使用了 applicationDataDirectory 下的子目录。 这是建议的方法，因为缓存包含暂时性数据。

### <a name="naming-video-assets-or-files"></a>命名视频资产或文件

通过媒体图，可以在云中创建资产或在边缘上创建 mp4 文件。 媒体资产可以由[连续视频录制](continuous-video-recording-tutorial.md)或[基于事件的视频录制](event-based-video-recording-tutorial.md)生成。 虽然可以根据需要对这些资产和文件命名，但对于基于连续视频录制的媒体资产，建议的命名结构是 "&lt;anytext&gt;-${System.GraphTopologyName}-${System.GraphInstanceName}"。 例如，可以对资产接收器设置 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

对于基于事件的视频录制生成的资产，建议的命名模式是 "&lt;anytext&gt;-${System.DateTime}"。 系统变量可确保当事件同时发生时，资产不会被覆盖。 例如，可以对资产接收器设置 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

如果运行同一图形的多个实例，可以使用图形拓扑名称和实例名称来加起区分。 例如，可以对资产接收器设置 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName} -${System.DateTime}"
```

对于边缘上基于事件的视频录制生成的 mp4 视频剪辑，建议的命名模式应包括 DateTime，对于同一图形的多个实例，建议使用系统变量 GraphTopologyName 和 GraphInstanceName。 例如，可以对文件接收器设置 filePathPattern，如下所示： 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

或 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>保持 VM 清洁

如果不定期对用作边缘设备的 Linux VM 进行管理，它可能会变得无响应。 因此，务必要保持缓存的清洁、清除不必要的包，并且从 VM 中删除未使用的容器。 为此，可以在边缘 VM 上使用下面一组建议的命令。

1. `sudo apt-get clean`

    apt-get clean 命令清理留在 /var/cache 中的检索的包文件的本地存储库。 它清理的目录是 /var/cache/apt/archives/ 和 /var/cache/apt/archives/partial/。 它在 /var/cache/apt/archives 中保留的唯一文件是锁定文件和部分子目录。 apt-get clean 命令通常用于根据需要清理磁盘空间，通常作为定期计划性维护的一部分。 有关详细信息，请参阅[使用 apt-get 进行清理](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)。
1. `sudo apt-get autoclean`

    与 apt-get clean 一样，apt-get autoclean 将清理检索的包文件的本地存储库，但它只会删除无法再下载且没有用的文件。 它有助于防止缓存变得太大。
1. `sudo apt-get autoremove1`

    自动删除选项将删除自动安装的包，因为其他的一些包需要这些自动安装的包，但在那些需要它们的包被删除后，将不再需要它们了
1. `sudo docker image ls` - 提供边缘系统上的 Docker 映像列表
1. `sudo docker system prune `

    Docker 采用保守的方法来清理未使用的对象 (通常称为 "垃圾回收" ) ，如映像、容器、卷和网络：这些对象通常不会被删除，除非明确要求 Docker 这样做。 这可能导致 Docker 会使用额外的磁盘空间。 对于每种类型的对象，Docker 都提供了 prune 命令。 此外，还可以使用 docker system prune 一次性清理多种类型的对象。 有关详细信息，请参阅[删除未使用的 Docker 对象](https://docs.docker.com/config/pruning/)。
1. `sudo docker rmi REPOSITORY:TAG`

    当边缘模块在进行更新时，docker 仍可有旧版本的边缘模块。 在这种情况下，建议使用 docker rmi 命令删除映像版本标记标识的特定映像。

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md)
