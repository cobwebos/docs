---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 590e5494a8c8f9d4e06b69af0708e83d53be72b5
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943852"
---
处理压缩音频是使用[适用 gstreamer](https://gstreamer.freedesktop.org)实现的。 出于许可原因，适用 gstreamer 二进制文件不会进行编译和与语音 SDK 关联。 开发人员需要安装几个依赖项和插件。

# <a name="ubuntu-1604-1804-or-debian-9"></a>[Ubuntu、16.04、18.04 或 Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rehl--centos"></a>[REHL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> 在 RHEL/CentOS 上，按照[如何为 Linux 配置 OpenSSL](../../../../how-to-configure-openssl-linux.md)的说明进行操作。

---