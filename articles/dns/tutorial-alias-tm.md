---
title: 教程：创建别名记录以支持域顶点名 - 流量管理器
titleSuffix: Azure DNS
description: 本教程演示如何配置 Azure DNS 别名记录，以使用流量管理器来支持对域顶点名的使用。
services: dns
author: asudbring
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: allensu
ms.openlocfilehash: 3834b782be054611de67b782b7fcd0c46cbf3a19
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082256"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>教程：使用流量管理器配置支持顶点域名的别名记录 

可以为域名顶点创建别名记录，以引用 Azure 流量管理器配置文件。 例如 contoso.com。 请配置 Azure DNS 以直接从区域中引用流量管理器配置文件，不需使用重定向服务。 


本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建主机 VM 和网络基础结构。
> * 创建流量管理器配置文件。
> * 创建别名记录。
> * 测试别名记录。


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
必须具有可用于在 Azure DNS 中托管以供测试的域名。 必须能够完全控制此域。 完全控制包括能够为域设置名称服务器 (NS) 记录。

有关如何在 Azure DNS 中托管域的说明，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。

本教程中使用的示例域为 contoso.com，但请使用自己的域名。

## <a name="create-the-network-infrastructure"></a>创建网络基础结构
首先，创建要放置 Web 服务器的虚拟网络和子网。
1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 在门户的左上角，选择“创建资源”。  在搜索框中输入“资源组”，然后创建名为 RG-DNS-Alias-TM 的资源组   。
3. 选择“创建资源” > “网络” > “虚拟网络”。   
4. 创建名为“VNet-Servers”的虚拟网络。  将其放在 RG-DNS-Alias-TM 资源组中，然后将子网命名为 SN-Web   。

## <a name="create-two-web-server-virtual-machines"></a>创建两个 Web 服务器虚拟机
1. 选择“创建资源” > “Windows Server 2016 VM”   。
2. 输入名称“Web-01”  ，然后将 VM 放在“RG-DNS-Alias-TM”  资源组中。 输入用户名和密码，然后选择“确定”。 
3. 对于“大小”，请选择具有 8 GB RAM 的 SKU  。
4. 对于“设置”  ，请选择“VNet-Servers”  虚拟网络和“SN-Web”  子网。
5. 选择“公共 IP 地址”。  在“分配”  下选择“静态”  ，然后选择“确定”。 
6. 对于公共入站端口，请选择“HTTP” > “HTTPS” > “RDP (3389)”，然后选择“确定”     。
7. 在“摘要”  页中，选择“创建”  。 此过程需要几分钟才能完成。

重复此过程，创建名为 Web-02 的另一个虚拟机  。

### <a name="add-a-dns-label"></a>添加 DNS 标签
公共 IP 地址需要 DNS 标签，以使用流量管理器。
1. 在 RG-DNS-Alias-TM 资源组中，选择 Web-01-ip 公共 IP 地址   。
2. 在“设置”下，选择“配置”   。
3. 在 DNS 名称标签文本框中，输入 web01pip  。
4. 选择“保存”。 

使用 DNS 名称标签 web02pip，对 Web-02-ip 公共 IP 地址重复此过程   。

### <a name="install-iis"></a>安装 IIS

同时在 Web-01 和 Web 02 上安装 IIS   。

1. 连接到 Web-01 并登录  。
2. 在“服务器管理器”仪表板上，选择“添加角色和功能”。  
3. 选择“下一步”三次。  在“服务器角色”页上，选择“Web 服务器(IIS)”。  
4. 选择“添加功能”，然后选择“下一步”   。
5. 选择“下一步”四次。  然后选择“安装”  。 此过程需要几分钟才能完成。
6. 安装完成后，选择“关闭”  。
7. 打开 Web 浏览器。 浏览到“localhost”  以验证是否显示默认 IIS 网页。

重复此过程以在 Web-02 上安装 IIS  。


## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

1. 打开 RG-DNS-Alias-TM 资源组，选择 Web-01-ip 公共 IP 地址   。 请记下 IP 地址供稍后使用。 对 Web-02-ip 公共 IP 地址重复此步骤  。
1. 选择“创建资源” > “网络” > “流量管理器配置文件”    。
2. 输入 **TM-alias-test** 作为名称。 将其放在 RG-DNS-Alias-TM 资源组中  。
3. 选择“创建”  。
4. 部署完成后，选择“转到资源”。 
5. 在“流量管理器配置文件”页的“设置”下，选择“终结点”   。
6. 选择 **添加** 。
7. 对于“类型”，请选择“外部终结点”；对于“名称”，请输入 EP-Web01     。
8. 在“完全限定的域名(FQDN)或 IP”文本框中，输入之前备注的 Web-01-ip 的 IP 地址   。
9. 选择其他资源所在的同一“位置”，然后选择“确定”   。

使用之前备注的 Web-02-ip 的 IP 地址，重复此过程以添加 Web-02 终结点   。

## <a name="create-an-alias-record"></a>创建别名记录

创建指向流量管理器配置文件的别名记录。

1. 选择 Azure DNS 区域以打开该区域。
2. 选择“记录集”。 
3. 将表示域名顶点的“名称”文本框留空  。 例如 contoso.com。
4. 将“类型”保留为 A 记录   。
5. 选择“别名记录集”复选框  。
6. 选择“选择 Azure 服务”，然后选择 TM-alias-test 流量管理器配置文件   。

## <a name="test-the-alias-record"></a>测试别名记录

1. 在 Web 浏览器中，浏览到域名顶点。 例如 contoso.com。 此时会看到 IIS 默认网页。 关闭 Web 浏览器。
2. 关闭 **Web-01** 虚拟机。 等待几分钟，让其完全关闭。
3. 打开新的 Web 浏览器，并再次浏览到域名顶点。
4. 此时会再次看到 IIS 默认网页，因为流量管理器已处理该情况并将流量定向到 Web-02  。

## <a name="clean-up-resources"></a>清理资源

不再需要为本教程创建的资源时，请删除 RG-DNS-Alias-TM 资源组  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建别名记录，以使用顶点域名来引用流量管理器配置文件。 若要了解 Azure DNS 和 Web 应用，请继续学习适用于 Web 应用的教程。

> [!div class="nextstepaction"]
> [在区域顶点托管负载均衡 Web 应用](./dns-alias-appservice.md)
