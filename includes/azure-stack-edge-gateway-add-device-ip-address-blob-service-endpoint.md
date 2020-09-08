---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080148"
---
1. 转到设备的本地 Web UI 并登录到设备。 确保设备处于解锁状态。

2. 转到“网络设置”页。 记下用来连接到客户端的网络接口的设备 IP 地址。

3. 如果使用远程 Windows 客户端，请以管理员身份启动记事本，然后打开位于 `C:\Windows\System32\Drivers\etc` 的 hosts 文件。

4. 将以下条目添加到 hosts 文件中：`<Device IP address> <Blob service endpoint>`

    你从 Azure 门户中创建的 Edge 存储帐户获取了 blob 服务终结点。 你将仅使用 blob 服务终结点的后缀。

    请使用以下图像作为参考。 保存 `hosts` 文件。

    ![在 Windows 客户端上修改 hosts 文件](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)