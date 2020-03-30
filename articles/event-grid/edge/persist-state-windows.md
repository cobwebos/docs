---
title: Windows 中的持久状态 - Azure 事件网格 IoT 边缘 |微软文档
description: 在 Windows 中保持状态
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086624"
---
# <a name="persist-state-in-windows"></a>在 Windows 中保持状态

默认情况下，在事件网格模块中创建的主题和订阅存储在容器文件系统中。 如果没有持久性，如果重新部署模块，则创建的所有元数据都将丢失。 要跨部署和重新启动保留数据，您需要将数据保留在容器文件系统之外。 

默认情况下，仅保留元数据，并且事件仍存储在内存中以提高性能。 请按照持久化事件部分启用事件持久性。

本文提供了在 Windows 部署中具有持久性的部署事件网格模块所需的步骤。

> [!NOTE]
>事件网格模块在 Windows 中作为低特权用户**容器用户**运行。

## <a name="persistence-via-volume-mount"></a>通过卷装载的持久性

[Docker 卷](https://docs.docker.com/storage/volumes/)用于跨部署保留数据。 要装载卷，您需要使用 docker 命令创建卷，授予权限，以便容器可以读取、写入该卷，然后部署模块。

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

   样品输出：-

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
1. 将 **"用户"** 组添加到 **"安装点**"指向的值，如下所示：
    1. 启动文件资源管理器。
    1. 导航到**Mountpoint**指向的文件夹。
    1. 右键单击，然后选择 **"属性**"。
    1. 选择 **"安全**"。
    1. 在 "组"或"用户名"下，选择 **"编辑**"。
    1. 选择 **"添加**"，输入`Users`"**选中"名称**"，然后选择 **"确定**"。
    1. 在 *"用户权限*"下，选择 **"修改**"，然后选择 **"确定**"。
1. 使用**绑定**装载此卷并从 Azure 门户重新部署事件网格模块

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
   >不要更改绑定值的第二部分。 它指向模块中的特定位置。 对于窗口上的事件网格模块，它必须是**C：\\\\应用元数据Db**。


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

您可以在主机系统上创建目录并装载该目录，而不是安装卷。

1. 通过运行以下命令在主机文件系统上创建目录。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用 **"绑定**"装载目录并从 Azure 门户重新部署事件网格模块。

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
    >不要更改绑定值的第二部分。 它指向模块中的特定位置。 对于窗口上的事件网格模块，它必须是**C：\\\\应用元数据Db**。

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
## <a name="persist-events"></a>持久事件

要启用事件持久性，必须首先使用上述部分通过卷装载或主机目录装载启用事件持久性。

关于持久事件需要注意的重要事项：

* 坚持事件根据每个事件订阅启用，并在装载卷或目录后选择加入。
* 事件持久性在创建时配置在事件订阅上，创建事件订阅后无法修改。 要切换事件持久性，必须删除并重新创建事件订阅。
* 持久化事件几乎总是比内存操作慢，但是速度差异在很大程度上取决于驱动器的特征。 速度和可靠性之间的权衡是所有邮件系统固有的，但只是大规模变得明显。

要在事件订阅上启用事件持久性，应`persistencePolicy`设置为`true`：

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