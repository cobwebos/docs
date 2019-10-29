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
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992257"
---
# <a name="persist-state-in-windows"></a>Windows 中的持久状态

在事件网格模块中创建的主题和订阅默认存储在容器文件系统中。 如果不具有持久性，则在重新部署该模块时，创建的所有元数据都将丢失。 若要跨部署保存数据，需要将数据保留在容器文件系统之外。 目前，只有元数据是持久的。 事件存储在内存中。 如果重新部署或重新启动事件网格模块，则任何未传递的事件都将丢失。

本文提供部署 Windows 部署中具有持久性的事件网格模块所需的步骤。

> [!NOTE]
>事件网格模块作为低权限用户**ContainerUser**在 Windows 中运行。

## <a name="persistence-via-volume-mount"></a>通过卷装入持久保存

[Docker 卷](https://docs.docker.com/storage/volumes/)用于在部署之间保留数据。 若要装载卷，需要使用 docker 命令创建它，并授予权限，以便容器能够读取、写入它，然后部署模块。 没有用于在 Windows 上自动创建具有所需权限的卷的预配。 需要在部署之前创建。

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
                "myeventgridvol:C:\\app\\metadataDb"
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

或者，你可以选择在主机系统上创建一个目录并装载该目录。

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
