---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046921"
---
### <a name="delete-local-resources"></a>删除本地资源

若要从设备中删除 IoT Edge 运行时和相关的资源，请使用适合设备操作系统的命令。 

#### <a name="windows"></a>Windows

卸载 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

删除 IoT Edge 运行时以后，已创建的容器会被停止，但仍存在于设备上。 查看所有容器。

   ```powershell
   docker ps -a
   ```

删除在设备上创建的运行时容器。

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

按照容器名称删除在 `docker ps` 输出中列出的任何其他容器。 

#### <a name="linux"></a>Linux

删除 IoT Edge 运行时。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

删除 IoT Edge 运行时以后，已创建的容器会被停止，但仍存在于设备上。 查看所有容器。

   ```bash
   sudo docker ps -a
   ```

删除在设备上创建的运行时容器。

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

按照容器名称删除在 `docker ps` 输出中列出的任何其他容器。 

删除容器运行时。

   ```bash
   sudo apt-get remove --purge moby
   ```