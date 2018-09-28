---
title: 教程 - 创建表示 Azure 公共 IP 地址的 Azure DNS 别名记录。
description: 本教程介绍如何配置表示 Azure 公共 IP 地址的 Azure DNS 别名记录。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991212"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>教程：配置表示 Azure 公共 IP 地址的别名记录 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建网络基础结构
> * 创建 Web 服务器虚拟机
> * 创建别名记录
> * 测试别名记录


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
必须具有可用于在 Azure DNS 中托管以供测试的域名。 必须对此域具有完全控制权限，包括能够为域设置名称服务器 (NS) 记录。

有关在 Azure DNS 中托管域的说明，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。

本教程中使用的示例域为 contoso.com，但应使用自己的域名。

## <a name="create-the-network-infrastructure"></a>创建网络基础结构
首先，创建要放置 Web 服务器的 VNet 和子网。
1. 通过 http://portal.azure.com 登录到 Azure 门户。
2. “”在门户的左上角，单击“创建资源”，在搜索框中键入“资源组”，然后创建名为“RG-DNS-Alias-pip” 的资源组。
3. 依次单击“创建资源”、“网络”和“虚拟网络”。
4. 创建名为“VNet-Server” 的虚拟网络，将其放在“RG-DNS-Alias-pip”资源组中，然后将子网命名为“SN-Web”。

## <a name="create-a-web-server-virtual-machine"></a>创建 Web 服务器虚拟机
1. 单击“创建资源”，并单击“Windows Server 2016 VM”。
2. 键入名称“Web-01”，然后将 VM 放在“RG-DNS-Alias-TM”资源组中。 键入用户名、密码，然后单击“确定”。
3. 对于“大小”，选择具有 8 GB RAM 的 SKU。
4. 对于“设置”，选择“VNet-Servers”虚拟网络、“SN-Web”子网。 对于公共入站端口，选择 **HTTP**、**HTTPS** 和 **RDP (3389)**，然后单击“确定”。
5. 在“摘要”页上，单击“创建”。

   此过程需要花费几分钟时间才能完成。

### <a name="install-iis"></a>安装 IIS

在 **Web-01** 上安装 IIS

1. 连接到 **Web-01** 并登录。
2. 在“服务器管理器”仪表板中，单击“添加角色和功能”。
3. 单击“下一步”三次，然后在“服务器角色”页上选择“Web 服务器(IIS)”。
4. 单击“添加功能”，并单击“下一步”。
5. 单击“下一步”四次，并单击“安装”。

   完成此设置可能需要几分钟时间。
6. 安装完成后，单击“关闭”。
7. 打开 Web 浏览器并浏览到“localhost”以验证是否显示默认 IIS 网页。

## <a name="create-an-alias-record"></a>创建别名记录

创建指向公共 IP 地址的别名记录。

1. 单击 Azure DNS 区域以打开该区域。
2. 单击“记录集”。
3. 在“名称”文本框中键入“web01”。
4. 将“类型”保留为 **A** 记录。
5. 单击“别名记录集”复选框。
6. 单击“选择 Azure 服务”，然后选择“Web-01-ip”公共 IP 地址。

## <a name="test-the-alias-record"></a>测试别名记录

1. 在“RG-DNS-Alias-pip”资源组中，单击“Web-01”虚拟机。 记录公共 IP 地址。
1. 在 Web 浏览器中，浏览到 Web01-01 虚拟机的完全限定域名。 例如，**web01.contoso.com**。 此时会看到 IIS 默认网页。
2. 关闭 Web 浏览器。
3. 停止“Web-01” 虚拟机，然后将其重新启动。
4. 重新启动后，记录虚拟机的新公共 IP 地址。
5. 打开 Web 浏览器并再次浏览到 Web01-01 虚拟机的完全限定域名。 例如，**web01.contoso.com**。
6. 此操作应当仍会成功，因为你使用了别名记录指向公共 IP 地址资源，而非标准的 A 记录。

## <a name="clean-up-resources"></a>清理资源

不再需要时，可以通过删除“RG-DNS-Alias-pip” 资源组来删除在本教程中创建的所有资源。


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建表示 Azure 公共 IP 地址的别名记录。 若要了解 Azure DNS 和 Web 应用，请继续学习适用于 Web 应用的教程。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
