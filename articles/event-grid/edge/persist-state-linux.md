---
title: 在 Linux 中持久保存状态-Azure 事件网格 IoT Edge |Microsoft Docs
description: 在 Linux 中持久保存元数据
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 39b16c6cfd5b94d412827ed88197edbef2da1453
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844626"
---
# <a name="persist-state-in-linux"></a>在 Linux 中持久保存状态

默认情况下，在事件网格模块中创建的主题和订阅存储在容器文件系统中。 如果不具有持久性，则在重新部署该模块时，创建的所有元数据都将丢失。 若要在部署和重启之间保留数据，需要将数据保留在容器文件系统之外。

默认情况下，只保存元数据，事件仍存储在内存中，以提高性能。 按照 "持久事件" 部分，启用事件持久性。

本文提供了在 Linux 部署中部署具有持久性的事件网格模块的步骤。

> [!NOTE]
>事件网格模块以具有 UID `2000` 和名称 `eventgriduser`的低特权用户身份运行。

## <a name="persistence-via-volume-mount"></a>通过卷装入持久保存

 [Docker 卷](https://docs.docker.com/storage/volumes/)用于在部署之间保留数据。 可以让 docker 自动创建命名卷，作为部署事件网格模块的一部分。 此选项是最简单的选项。 可以在 "**绑定**" 部分指定要创建的卷名，如下所示：

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
>不要更改绑定值的第二部分。 它指向模块内的特定位置。 对于 Linux 上的事件网格模块，必须 **/app/metadataDb**。

例如，下面的配置将导致创建**egmetadataDbVol** ，其中将保留元数据。

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
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

你可以在主机系统上创建一个目录并装载该目录，而不是装载卷。

## <a name="persistence-via-host-directory-mount"></a>通过主机目录装载的持久性

你还可以选择装载主机文件夹，而不是 docker 卷。

1. 首先，通过运行以下命令在主机计算机上创建名称为**eventgriduser** 、ID 为**2000**的用户：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 通过运行以下命令在主机文件系统上创建一个目录。

   ```sh
   md <your-directory-name-here>
   ```

    例如，运行以下命令将创建一个名为**myhostdir**的目录。

    ```sh
    md /myhostdir
    ```
1. 接下来，请运行以下命令，使**eventgriduser**此文件夹的所有者。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用**绑定**来装入目录，并从 Azure 门户重新部署事件网格模块。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    例如，

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
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
    >不要更改绑定值的第二部分。 它指向模块内的特定位置。 对于 linux 上的事件网格模块，必须 **/app/metadata**。


## <a name="persist-events"></a>持久保存事件

若要启用事件持久性，必须首先使用上述部分通过卷装载或主机目录装载启用元数据持久性。

有关保留事件的重要事项：

* 在每个事件订阅的基础上启用保留事件，并在装入卷或目录后选择加入。
* 事件持久性在创建时配置在事件订阅上，并且在创建事件订阅后无法修改。 若要切换事件持久性，必须删除并重新创建事件订阅。
* 保持事件的速度几乎始终比在内存操作中慢，但速度差异很大程度上取决于驱动器的特征。 速度和可靠性之间的权衡对于所有消息系统都是固有的，但通常只是大规模 noticible。

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