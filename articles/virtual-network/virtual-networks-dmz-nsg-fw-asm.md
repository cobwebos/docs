---
title: 外围网络示例 – 构建外围网络使用防火墙保护应用程序和 Nsg |Microsoft Docs
description: 生成具有防火墙的外围网络和网络安全组 (Nsg)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868304"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>示例 2：构建外围网络以通过防火墙和 Nsg 保护应用程序
[返回到 Microsoft 云服务和网络安全页][HOME]

此示例演示如何创建外围网络 (也称为*外围网络*，*外围安全区域*，并*外围子网*) 具有防火墙，四个 Windows Server 计算机，和网络安全组 (Nsg)。 它包括有关每个相关的命令，提供更深入地了解每个步骤的详细信息。 "流量方案"部分提供了流量如何流经外围网络中的防御层的分步说明。 最后，"参考"部分提供了完整的代码和有关如何构建此环境来测试和试验不同的方案的说明。

![使用 NVA 和 Nsg 的入站的外围网络][1]

## <a name="environment"></a>环境 
此示例基于包含以下各项的 Azure 订阅使用的方案：

* 两个云服务：FrontEnd001 和 BackEnd001。
* 名为 CorpNetwork 具有两个子网的虚拟网络：前端和后端。
* 一个 NSG 应用于这两个子网。
* 网络虚拟设备：Barracuda NextGen 防火墙连接到前端子网。
* 表示应用程序 web 服务器的 Windows Server 计算机：IIS01.
* 代表应用程序后端服务器的两个 Windows Server 计算机：AppVM01 和 AppVM02。
* Windows Server 计算机，一个代表 DNS 服务器：DNS01.

> [!NOTE]
> 虽然此示例中使用的 Barracuda NextGen Firewall，但可以使用许多网络虚拟设备。
> 
> 

这篇文章的"参考"部分中的 PowerShell 脚本生成的大多数环境此处所述。 Vm 和虚拟网络生成的脚本，但这些进程不本文档中的详细信息中所述。

构建环境的方法如下：

1. 保存网络配置 XML 文件包含的"参考"部分中 （使用更新的名称、 位置和 IP 地址以匹配你的方案）。
2. 更新 PowerShell 脚本，以匹配对 （订阅、 服务名称等） 运行该脚本的环境中的用户定义变量。
3. 在 PowerShell 中运行该脚本。

> [!NOTE]
> PowerShell 脚本中指定区域必须与网络配置 XML 文件中指定的区域相匹配。
>
>

该脚本成功运行后，可以完成以下步骤：

1. 设置防火墙规则。 请参阅这篇文章的"防火墙规则"部分。
2. 如果你想，您可以设置 web 服务器和使用简单的 web 应用程序，以便外围网络配置测试的应用程序服务器。 可以使用"参考"部分中提供的两个应用程序脚本。

下一部分介绍的大部分与 Nsg 相关的脚本的语句。

## <a name="nsgs"></a>NSG
此示例将构建一个 NSG 组，然后加载六个规则。

> [!TIP]
> 一般情况下，应首先创建特定的"允许"规则和最后一个更通用的"拒绝"规则。 先评估哪些规则分配的优先级控件。 它应用于特定的规则找到流量后，没有更多的规则进行评估。 可以在入站或出站方向 （从子网的角度来看） 应用 NSG 规则。
> 
> 

以声明方式，这些规则是对入站流量：

1. 允许内部 DNS 流量（端口 53）。
2. 允许 RDP 流量 （端口 3389） 从 internet 到任何 VM。
3. 允许 HTTP 流量 （端口 80） 从 internet 到 NVA （防火墙）。
4. 允许从 IIS01 到 AppVM01 有所有流量 （所有端口）。
5. 拒绝从 internet 到整个虚拟网络 （两个子网） 的所有流量 （所有端口）。
6. 拒绝从前端子网到后端子网的所有流量 （所有端口）。

这些规则绑定到每个子网，如果从 internet 到 web 服务器，入站 HTTP 请求都规则 3 （允许） 和规则 5 （拒绝） 看起来要应用，但由于规则 3 具有较高的优先级，它将仅应用。 规则不会派上用场。 因此 HTTP 请求将允许到防火墙。

如果相同的流量尝试传往 DNS01 服务器，规则 5 （拒绝） 则会先应用，因此不会允许该流量传递到服务器。 规则 6 （拒绝） 阻止前端子网与后端子网 （除外规则 1 和 4 允许的流量） 进行通信。 如果攻击者入侵前端上的 web 应用程序，这样可以使后端网络。 在这种情况下，攻击者会限制后端的"受保护"网络访问权限。 （攻击者将能够访问只能访问 AppVM01 服务器上公开的资源。）

没有允许流量外流到 internet 的默认出站规则。 在此示例中，我们允许出站流量和未修改任何出站规则。 若要锁定两个方向的流量，需要用户定义的路由。 您可以了解有关在另一示例中这种方法[主要安全边界文档][HOME]。

此处所述的 NSG 规则包括类似于中的 NSG 规则[示例 1-构建简单的外围网络使用 Nsg][Example1]。 查看每个 NSG 规则，其属性的详细信息那篇文章中的 NSG 说明。

## <a name="firewall-rules"></a>防火墙规则
需要管理防火墙并创建所需的配置的计算机上安装管理客户端。 请参阅有关如何管理设备的文档从你的防火墙 （或其他 NVA） 供应商。 本部分的其余部分介绍如何配置防火墙本身通过供应商的管理客户端 （不在 Azure 门户或 PowerShell）。

请参阅[Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)有关客户端下载并连接到此示例中使用的 Barracuda 防火墙的说明。

您需要在防火墙上创建转发规则。 因为在此示例中的方案只将 internet 流量路由到防火墙入站和到 web 服务器，则只需要一个转发 NAT 规则。 在此示例中使用 Barracuda 防火墙中，规则就是目标 NAT 规则 (Dst NAT) 传递此流量。

若要创建以下规则 （或验证现有的默认规则），请完成以下步骤：
1. 在 Barracuda NG Admin 客户端仪表板中，在配置选项卡上，在**操作配置**部分中，选择**Ruleset**。 

   一个网格，称为**主规则**显示现有活动和停用在防火墙上的规则。

2. 若要创建新规则，请选择较小的绿色**+** 此网格右上角的按钮。 （你的防火墙可能被锁定。 如果看到标记为的按钮**锁**并且无法创建或编辑规则，请选择此按钮以解除锁定规则集并允许编辑。)
  
3. 若要编辑现有规则，请选择规则，右键单击，并选择**编辑规则**。

创建新的规则名称类似于**WebTraffic。** 

目标 NAT 规则图标类似于：![目标 NAT 图标][2]

规则本身将如下所示：

![防火墙规则][3]

任何到达防火墙并尝试以连接到 HTTP （端口 80 或 443 用于 HTTPS） 的入站的地址将从防火墙的 DHCP1 本地 IP 接口发出，并定向到 IP 地址为 10.0.1.5 的 web 服务器。 由于流量是传入端口 80 和端口 80 上转到 web 服务器，不需要更改任何端口。 但是，目标列表可能是 10.0.1.5:8080 如果 web 服务器在端口 8080，将其转换的入站的端口 80 在防火墙上的 web 服务器上的入站端口 8080 上侦听。

你还应指定一种连接方法。 对于来自 internet 的目标规则，动态 SNAT 是最合适的方法。

即使仅创建一条规则后，务必正确设置其优先级。 在防火墙上的所有规则的网格中，如果此新规则位于底部 （下面 BLOCKALL 规则），它将永远不会派上用场。 请确保 web 流量的新规则是 BLOCKALL 规则之上。

创建规则后，您需要将其推送到防火墙并随后激活它。 如果不采取这些步骤，规则更改将不会生效。 下一节介绍了推送和激活过程。

## <a name="rule-activation"></a>规则激活
现在，将规则添加到规则集，您需要将规则集上传到防火墙并将其激活。

![防火墙规则激活][4]

在右上角的管理客户端，您将看到一组按钮。 选择**发送更改**以修改规则集发送到防火墙，然后选择**激活**。

现在，你已激活防火墙规则集，该环境已完成。 如果你想，您可以"参考"部分添加到环境的应用程序中运行示例应用程序脚本。 如果添加应用程序，可以测试下一节中所述的流量方案。

> [!IMPORTANT]
> 您应该意识到不直接命中 web 服务器。 当浏览器从 FrontEnd001.CloudApp.Net 请求 HTTP 页面时，HTTP 终结点 （端口 80） 将流量传递到防火墙上，而不是 web 服务器。 防火墙然后，由于前面创建的规则，而使用 NAT 将请求映射到 web 服务器。
> 
> 

## <a name="traffic-scenarios"></a>流量方案
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>（允许）Web 访问 web 服务器通过防火墙
1. Internet 用户从 FrontEnd001.CloudApp.Net （面向 internet 的云服务） 请求 HTTP 页面。
2. 云服务将端口 80 上通过开放的终结点的流量传递到 10.0.1.4: 80 上的防火墙的本地接口。
3. 前端子网开始处理入站的规则：
   1. NSG 规则 1 (DNS) 不适用。 转到下一步的规则。
   2. NSG 规则 2 (RDP) 不适用。 转到下一步的规则。
   3. 应用 NSG 规则 3(internet 到防火墙)。 允许的流量。 停止处理规则。
4. 流量抵达防火墙 (10.0.1.4) 的内部 IP 地址。
5. 防火墙转发规则确定这是端口 80 通信，并将它重定向到 web 服务器 IIS01。
6. IIS01 正在侦听的 web 流量、 收到请求时，并开始处理请求。
7. IIS01 请求 AppVM01 上的 SQL Server 实例的信息。
8. 没有出站规则前, 端子网上，因此允许流量。
9. 后端子网开始处理入站的规则：
   1. NSG 规则 1 (DNS) 不适用。 转到下一步的规则。
   2. NSG 规则 2 (RDP) 不适用。 转到下一步的规则。
   3. NSG 规则 3(internet 到防火墙) 不适用。 转到下一步的规则。
   4. NSG 规则 4 (IIS01 到 AppVM01) 适用。 允许的流量。 停止处理规则。
10. AppVM01 上的 SQL Server 实例接收请求和响应。
11. 由于在后端子网上没有出站规则，因此允许响应。
12. 前端子网开始处理入站的规则：
    1. 不存在 NSG 规则应用于入站流量从后端子网到前端子网，因此没有任何 NSG 规则同样适用。
    2. 允许子网之间的流量的默认系统规则允许此流量，因此允许流量。
13. IIS01 接收来自 AppVM01 的响应，完成 HTTP 响应，并将其发送给请求者。
14. 由于这是来自防火墙的 NAT 会话，则响应目标最初是针对防火墙。
15. 防火墙接收来自 web 服务器的响应，并将其转发回 internet 用户。
16. 由于前端子网上没有出站规则，允许响应，internet 用户将收到 web 页。

#### <a name="allowed-rdp-to-backend"></a>（允许）通过 rdp 连接到后端
1. 在 internet 上的服务器管理员请求与 AppVM01 上 BackEnd001.CloudApp.Net RDP 会话：*xxxxx*，其中*xxxxx* rdp 访问 AppVM01 是随机分配的端口号。 （您可以找到分配的端口在 Azure 门户或 PowerShell）。
2. 防火墙只在 FrontEnd001.CloudApp.Net 地址上侦听，因为它不涉及此流量流。
3. 后端子网开始处理入站的规则：
   1. NSG 规则 1 (DNS) 不适用。 转到下一步的规则。
   2. 应用 NSG 规则 2 (RDP)。 允许的流量。 停止处理规则。
4. 由于没有出站规则，则默认规则应用，并返回允许流量。
5. 已启用 RDP 会话。
6. AppVM01 提示输入用户名和密码。

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>（允许）DNS 服务器上的 web 服务器 DNS 查找
1. Web 服务器 IIS01 需要 www.data.gov 在源数据，但需要解析地址。
2. 网络配置的虚拟网络显示 DNS01 (后端子网上的 10.0.2.4) 作为主 DNS 服务器。 IIS01 将 DNS 请求发送到 DNS01。
3. 由于前端子网上没有出站规则，允许流量。
4. 后端子网开始处理入站的规则：
   1. NSG 规则 1 (DNS) 应用。 允许的流量。 停止处理规则。
5. DNS 服务器接收请求。
6. DNS 服务器没有缓存的地址，并查询在 internet 上的根 DNS 服务器。
7. 由于在后端子网上没有出站规则，允许流量。
8. Internet DNS 服务器做出响应。 由于会话从内部发起的因此允许响应。
9. DNS 服务器缓存响应，并响应从 IIS01 请求。
10. 由于在后端子网上没有出站规则，允许流量。
11. 前端子网开始处理入站的规则：
    1. 不存在 NSG 规则应用于入站流量从后端子网到前端子网，因此没有任何 NSG 规则同样适用。
    2. 允许子网之间的流量的默认系统规则允许此流量，因此允许该流量。
12. IIS01 从 DNS01 接收响应。

#### <a name="allowed-web-server-file-access-on-appvm01"></a>（允许）AppVM01 上的 web 服务器文件访问
1. IIS01 请求 AppVM01 上的文件。
2. 由于前端子网上没有出站规则，允许流量。
3. 后端子网开始处理入站的规则：
   1. NSG 规则 1 (DNS) 不适用。 转到下一步的规则。
   2. NSG 规则 2 (RDP) 不适用。 转到下一步的规则。
   3. NSG 规则 3(internet 到防火墙) 不适用。 转到下一步的规则。
   4. NSG 规则 4 (IIS01 到 AppVM01) 适用。 允许的流量。 停止处理规则。
4. AppVM01 接收请求，并做出响应 （假设授予的访问权限） 的文件。
5. 由于在后端子网上没有出站规则，因此允许响应。
6. 前端子网开始处理入站的规则：
   1. 不存在 NSG 规则应用于入站流量从后端子网到前端子网，因此没有任何 NSG 规则同样适用。
   2. 允许子网之间的流量的默认系统规则允许此流量，因此允许该流量。
7. IIS01 接收文件。

#### <a name="denied-web-direct-to-web-server"></a>（拒绝）Web 直接连接到 web 服务器
Web 服务器 IIS01 和防火墙都在同一个云服务，因为它们共享相同的面向公众的 IP 地址。 因此任何 HTTP 流量定向到防火墙。 虽然将成功处理请求，它不能直接转到 web 服务器。 该测试通过，按照设计，通过防火墙第一次。 请参阅在本部分中的流量流的第一个方案。

#### <a name="denied-web-to-backend-server"></a>（拒绝）Web 访问后端服务器
1. Internet 用户尝试通过 BackEnd001.CloudApp.Net 服务访问 AppVM01 上的文件。
2. 由于没有为文件共享开放终结点，这不会通过云服务，并且不会到达服务器。
3. 如果出于某种原因处于打开状态的终结点，NSG 规则 5(internet 到虚拟网络) 阻止了流量。

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>（拒绝）DNS 服务器上的 web DNS 查找
1. Internet 用户尝试查找 DNS01 上通过 BackEnd001.CloudApp.Net 服务的内部 DNS 记录。
2. 由于没有为 DNS 开放终结点，这不会通过云服务，并且不会到达服务器。
3. 如果出于某种原因处于打开状态的终结点，NSG 规则 5(internet 到虚拟网络) 阻止了流量。 （规则 1 [DNS] 不适用于以下两个原因。 首先，源地址是 internet，并仅当本地虚拟网络是源时，才应用此规则。 其次，此规则是允许规则，因此它永远不会拒绝流量。）

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>（拒绝）Web 通过防火墙访问 SQL
1. Internet 用户从 FrontEnd001.CloudApp.Net （面向 internet 的云服务） 请求 SQL 数据。
2. 由于没有为 SQL 开放终结点，这不会通过云服务，并且不会到达防火墙。
3. 如果出于某种原因，终结点处于打开状态前, 端子网开始处理入站的规则：
   1. NSG 规则 1 (DNS) 不适用。 转到下一步的规则。
   2. NSG 规则 2 (RDP) 不适用。 转到下一步的规则。
   3. 应用 NSG 规则 3(internet 到防火墙)。 允许的流量。 停止处理规则。
4. 流量抵达防火墙 (10.0.1.4) 的内部 IP 地址。
5. 防火墙不具有 SQL 的任何转发规则，将丢弃流量。

## <a name="conclusion"></a>结束语
此示例演示了相对较简单方法以保护应用程序使用防火墙和隔离后端子网与入站流量。

您可以找到更多示例和概述了网络安全边界[这里][HOME]。

## <a name="references"></a>参考
### <a name="full-script-and-network-config"></a>完整脚本和网络配置
在 PowerShell 脚本文件中保存的完整脚本。 将网络配置脚本保存在名为 NetworkConf2.xml 的文件。
根据需要更改用户定义变量。 运行该脚本，然后按照这篇文章的"防火墙规则"部分中的说明进行操作。

#### <a name="full-script"></a>完整脚本
此脚本中，基于用户定义的变量，将完成以下步骤：

1. 连接到 Azure 订阅。
2. 创建存储帐户。
3. 创建虚拟网络和两个子网，如网络配置文件中定义。
4. 生成四个 Windows Server Vm。
5. 配置 NSG。 配置完成以下步骤：
   * 创建 NSG。
   * 填充的 NSG 规则。
   * 将 NSG 绑定到相应的子网。

应在连接 internet 的计算机或服务器上本地运行此 PowerShell 脚本。

> [!IMPORTANT]
> 当运行此脚本时，在 PowerShell 中可能会出现警告和其他信息性消息。 只需关心如何以红色显示的错误消息。
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>网络配置文件
使用已更新的位置保存此 XML 文件，然后将链接添加到此文件在前面的脚本在 $NetworkConfigFile 变量中。

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>示例应用程序脚本
如果你想要为此示例和其他外围网络示例安装示例应用程序，请参阅[示例应用程序脚本][SampleApp]。

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "使用 NSG 的入站外围网络"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "“目标 NAT”图标"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "防火墙规则"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "防火墙规则激活"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
