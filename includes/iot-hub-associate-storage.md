---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: f88f6aa6daec342b24d165c4c4cc0a89a0805f7a
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558470"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>将 Azure 存储帐户关联到 IoT 中心

由于模拟设备应用将文件上传到 Blob，因此必须拥有与 IoT 中心关联的 [Azure 存储](../articles/storage/common/storage-quickstart-create-account.md)帐户。 将 Azure 存储帐户与 IoT 中心相关联时，IoT 中心会生成一个 SAS URI。 设备可以使用此 SAS URI 安全地将文件上传到 Blob 容器。 IoT 中心服务和设备 SDK 协调生成 SAS URI 的过程，并使其可供设备用来上传文件。

按照[使用 Azure 门户配置文件上传](../articles/iot-hub/iot-hub-configure-file-upload.md)中的说明进行操作。 确保有一个 Blob 容器与你的 IoT 中心关联并且文件通知已启用。

![在门户中启用文件通知](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
