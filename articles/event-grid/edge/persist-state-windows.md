---
title: 在 Windows 中保持状态-Azure 事件网格 IoT Edge |Microsoft Docs
description: Windows 中的持久状态
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 42f7b5315cecd75e2aaf67145c57982872f43550
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844609"
---
# <a name="persist-state-in-windows"></a>Windows 中的持久状态

默认情况下，在事件网格模块中创建的主题和订阅存储在容器文件系统中。 如果不具有持久性，则在重新部署该模块时，创建的所有元数据都将丢失。 若要在部署和重启之间保留数据，需要将数据保留在容器文件系统之外。 

默认情况下，只保存元数据，事件仍存储在内存中，以提高性能。 按照 "持久事件" 部分，启用事件持久性。

本文提供部署 Windows 部署中具有持久性的事件网格模块所需的步骤。

> [!NOTE]
>事件网格模块作为低权限用户**ContainerUser**在 Windows 中运行。

## <a name="persistence-via-volume-mount"></a>通过卷装入持久保存

[Docker 卷](https://docs.docker.com/storage/volumes/)用于在部署之间保留数据。 若要装载卷，需要使用 docker 命令创建它，并授予权限，以便容器能够读取、写入它，然后部署模块。

1. 通过运行以下命令创建卷：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 通过运行以下命令获取卷映射到的主机目录

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   示例输出：-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 将**用户**组添加到**装载**点指向的值，如下所示：
    1. 启动文件资源管理器。
    1. 导航到**装载**点指向的文件夹。
    1. 右键单击，然后选择 "**属性**"。
    1. 选择“安全性”。
    1. 在 "组或用户名" 下，选择 "**编辑**"。
    1. 选择 "**添加**"，输入 `Users`，选择 "**检查名称**"，然后选择 **"确定"** 。
    1. 在 "*用户的权限*" 下，选择 "**修改**"，然后选择 **"确定"** 。
1. 使用**绑定**来装入此卷，并从 Azure 门户重新部署事件网格模块

   例如，

    ```json
        {
              "Env": [
                "inbound__serverAuth__tlsPolicy=strict",
                "inbound__serverAuth__serverCert__source=IoTEdge",
                "inbound__clientAuth__sasKeys__enabled=false",
                "inbound__clientAuth__clientCert__enabled=true",
                "inbound__clientAuth__clientCert__source=IoTEdge",
                "inbound__clientAuth__clientCert__allowUnknownCA=true",
                "outbound__clientAuth__clientCert__enabled=true",
                "outbound__clientAuth__clientCert__source=IoTEdge",
                "outbound__webhook__httpsOnly=true",
                "outbound__webhook__skipServerCertValidation=false",
                "outbound__webhook__allowUnknownCA=true"
              ],
              "HostConfig": {
                "Binds": [
                  "<your-volume-name-here>:C:\\app\\metadataDb"
                ],
                "PortBindings": {
                  "4438/tcp": [
                     {
                        "HostPort": "4438"
                     }
                  ]
                }
              }
        }
    ```

   >[!IMPORTANT]
   >不要更改绑定值的第二部分。 它指向模块中的特定位置。 对于 windows 上的事件网格模块，必须是**C：\\应用\\metadataDb**。


    例如，

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```

## <a name="persistence-via-host-directory-mount"></a>通过主机目录装载的持久性

你可以在主机系统上创建一个目录并装载该目录，而不是装载卷。

1. 通过运行以下命令在主机文件系统上创建一个目录。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用**绑定**来装入目录，并从 Azure 门户重新部署事件网格模块。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >不要更改绑定值的第二部分。 它指向模块中的特定位置。 对于 windows 上的事件网格模块，该模块必须为**C：\\应用\\metadataDb**。

    例如，

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```
## <a name="persist-events"></a>持久保存事件

若要启用事件持久性，必须首先使用上述部分通过卷装载或主机目录装载启用元数据持久性。

有关保留事件的重要事项：

* 在每个事件订阅的基础上启用保留事件，并在装入卷或目录后选择加入。
* 事件持久性在创建时配置在事件订阅上，并且在创建事件订阅后无法修改。 若要切换事件持久性，必须删除并重新创建事件订阅。
* 保持事件的速度几乎始终比在内存操作中慢，但速度差异很大程度上取决于驱动器的特征。 速度和可靠性之间的权衡对于所有消息系统都是固有的，但它只是大规模 noticible。

若要在事件订阅中启用事件持久性，请将 `persistencePolicy` 设置为 `true`：

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```