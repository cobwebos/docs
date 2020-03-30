---
title: Linux 中的持久状态 - Azure 事件网格 IoT 边缘 |微软文档
description: Linux 中持久性元数据
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086651"
---
# <a name="persist-state-in-linux"></a>Linux 中的持久状态

默认情况下，在事件网格模块中创建的主题和订阅存储在容器文件系统中。 如果没有持久性，如果重新部署模块，则创建的所有元数据都将丢失。 要跨部署和重新启动保留数据，您需要将数据保留在容器文件系统之外。

默认情况下，仅保留元数据，并且事件仍存储在内存中以提高性能。 请按照持久化事件部分启用事件持久性。

本文提供了在 Linux 部署中具有持久性的事件网格模块部署的步骤。

> [!NOTE]
>事件网格模块以具有 UID`2000`和名称`eventgriduser`的低特权用户运行。

## <a name="persistence-via-volume-mount"></a>通过卷装载的持久性

 [Docker 卷](https://docs.docker.com/storage/volumes/)用于跨部署保留数据。 您可以让 Docker 自动创建命名卷，作为部署事件网格模块的一部分。 此选项是最简单的选项。 您可以指定要在 **"绑定"** 部分中创建的卷名称，如下所示：

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>不要更改绑定值的第二部分。 它指向模块中的特定位置。 对于 Linux 上的事件网格模块，它必须是 **/app/元数据Db**。

例如，以下配置将导致创建将保留元数据的卷 **（元数据DbVol）。**

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
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
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

您可以在主机系统上创建目录并装载该目录，而不是安装卷。

## <a name="persistence-via-host-directory-mount"></a>通过主机目录装载的持久性

您还可以选择装载主机文件夹，而不是 Docker 卷。

1. 首先通过运行以下命令在主机上创建名称**事件网格用户**和 ID **2000**的用户：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 通过运行以下命令在主机文件系统上创建目录。

   ```sh
   md <your-directory-name-here>
   ```

    例如，运行以下命令将创建一个名为**myhostdir 的**目录。

    ```sh
    md /myhostdir
    ```
1. 接下来，通过运行以下命令使**事件网格用户**拥有此文件夹。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用 **"绑定"** 装载目录并从 Azure 门户重新部署事件网格模块。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
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
    >不要更改绑定值的第二部分。 它指向模块中的特定位置。 对于 linux 上的事件网格模块，它必须是 **/app/元数据Db**和 **/app/eventDb**


## <a name="persist-events"></a>持久事件

要启用事件持久性，必须首先使用上述部分通过卷装载或主机目录装载启用元数据持久性。

关于持久事件需要注意的重要事项：

* 坚持事件根据每个事件订阅启用，并在装载卷或目录后选择加入。
* 事件持久性在创建时配置在事件订阅上，创建事件订阅后无法修改。 要切换事件持久性，必须删除并重新创建事件订阅。
* 持久化事件几乎总是比内存操作慢，但是速度差异在很大程度上取决于驱动器的特征。 速度和可靠性之间的权衡是所有邮件系统固有的，但通常只会在大规模时变得明显。

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
