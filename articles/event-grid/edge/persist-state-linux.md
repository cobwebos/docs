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
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100337"
---
# <a name="persist-state-in-linux"></a>在 Linux 中持久保存状态

在事件网格模块中创建的主题和订阅默认存储在容器文件系统中。 如果不具有持久性，则在重新部署该模块时，创建的所有元数据都将丢失。 目前仅保存元数据。 事件存储在内存中。 如果重新部署或重新启动事件网格模块，则任何未传递的事件都将丢失。

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
      "egmetadataDbVol:/app/metadataDb"
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

或者，可以使用 docker 客户端命令创建 docker 卷。 

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
                  "/myhostdir:/app/metadataDb"
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
