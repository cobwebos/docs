---
title: Azure VMware 解决方案（按云简单 ） - 为云简单私有云配置 DNS
description: 描述如何设置 DNS 名称解析，以便从本地工作站访问云简单私有云上的 vCenter 服务器
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246105"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>为来自本地工作站的私有云 vCenter 访问配置名称解析

要从本地工作站访问云简单私有云上的 vCenter 服务器，必须配置 DNS 地址解析，以便 vCenter 服务器可以通过主机名和 IP 地址进行寻址。

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>获取私有云的 DNS 服务器 IP 地址

1. 登录到[云简单门户](access-cloudsimple-portal.md)。

2. 导航到**资源** > **私有云**，然后选择要连接到的私有云。

3. 在 **"基本信息**"下的私有云**摘要**页上，复制私有云 DNS 服务器 IP 地址。

    ![私有云 DNS 服务器](media/private-cloud-dns-server.png)


将这些选项之一用于 DNS 配置。

* [在 DNS 服务器上为 *.cloudsimple.io 创建区域](#create-a-zone-on-a-microsoft-windows-dns-server)
* [在本地 DNS 服务器上创建条件转发器以解决 *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>在 DNS 服务器上为 *.cloudsimple.io 创建区域

您可以将区域设置为存根区域，并指向私有云上的 DNS 服务器以进行名称解析。 本节提供有关使用 BIND DNS 服务器或 Microsoft Windows DNS 服务器的信息。

### <a name="create-a-zone-on-a-bind-dns-server"></a>在 BIND DNS 服务器上创建区域

要配置的特定文件和参数可能因您的单个 DNS 设置而异。

例如，对于默认的 BIND 服务器配置，请在 DNS 服务器上编辑 /etc/命名.conf 文件并添加以下区域信息。

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>在 Microsoft Windows DNS 服务器上创建区域

1. 右键单击 DNS 服务器并选择 **"新区域**"。 
  
    ![新区](media/DNS01.png)
2. 选择**存根区域**，然后单击 **"下一步**"。

    ![新区](media/DNS02.png)
3. 根据您的环境选择适当的选项，然后单击 **"下一步**"。

    ![新区](media/DNS03.png)
4. 选择 **"前进查找区域**"，然后单击 **"下一步**"。

    ![新区](media/DNS01.png)
5. 输入区域名称，然后单击 **"下一步**"。

    ![新区](media/DNS05.png)
6. 输入从云简单门户获取的私有云 DNS 服务器的 IP 地址。

    ![新区](media/DNS06.png)
7. 根据需要单击 **"下一步**"以完成向导设置。

## <a name="create-a-conditional-forwarder"></a>创建条件转发器

条件转发器将所有 DNS 名称解析请求转发到指定的服务器。 通过此设置，对 @.cloudsimple.io的任何请求将转发到位于私有云上的 DNS 服务器。 以下示例演示如何在不同类型的 DNS 服务器上设置转发器。

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>在 BIND DNS 服务器上创建条件转发器

要配置的特定文件和参数可能因您的单个 DNS 设置而异。

例如，对于默认的 BIND 服务器配置，在 DNS 服务器上编辑 /etc/命名.conf 文件，并添加以下条件转发信息。

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>在 Microsoft Windows DNS 服务器上创建条件转发器

1. 打开 DNS 服务器上的 DNS 管理器。
2. 右键单击 **"条件转发器**"并选择添加新条件转发器的选项。

    ![条件转发器 1 视窗 DNS](media/DNS08.png)
3. 在私有云中输入 DNS 域和 DNS 服务器的 IP 地址，然后单击"**确定**"。
