---
title: 在 AWS 上创建群集的基础结构
description: 本教程介绍如何设置 AWS 基础结构来运行 Service Fabric 群集。
author: dkkapur
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: b6348d9fdcd4133a4fa69aac3ccf5494e904094e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75613969"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>教程：创建用于托管 Service Fabric 群集的 AWS 基础结构

Service Fabric 独立群集为你提供选择自己的环境的选项，并创建群集作为 Service Fabric 所采用的“任何 OS、任何云”方法的一部分。 在本系列教程中，我们将创建一个托管在 AWS 上的独立群集，并将应用程序安装到其中。

本教程是一个系列中的第一部分。 在本文中，我们将生成所需的 AWS 资源用于托管 Service Fabric 的独立群集。 在后续文章中，我们需要安装 Service Fabric 独立套件、将示例应用程序安装到群集，最后清理群集。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建一组 EC2 实例
> * 修改安全组
> * 登录到其中一个实例
> * 准备 Service Fabric 的实例

## <a name="prerequisites"></a>必备条件

若要完成本教程，需要一个 AWS 帐户。  如果没有帐户，请转到 [AWS 控制台](https://aws.amazon.com/)创建一个帐户。

## <a name="create-ec2-instances"></a>创建 EC2 实例

登录到 AWS 控制台，在搜索框中输入 **EC2**，然后选择“云中的 EC2 虚拟服务器” 

![AWS 控制台搜索][aws-console]

选择“启动实例”，在下一个屏幕中选择“Microsoft Windows Server 2016 Base”旁边的“选择”。  

![EC2 实例选择][aws-ec2instance]

依次选择“t2.medium”、“下一步: 配置实例详细信息”，在下一个屏幕中，将实例数目更改为 `3`，然后选择“高级详细信息”展开该部分。

若要在 Service Fabric 中将虚拟机连接到一起，托管基础结构的 VM 需有相同的凭据。  可通过两种常用方法获取一致的凭据：将所有 VM 加入同一个域，或者在每个 VM 上设置相同的管理员密码。  本教程使用用户数据脚本将所有 EC2 实例设置为采用相同的密码。  在生产环境中，将主机加入 Windows 域会更安全。

在控制台上的用户数据字段中输入以下脚本：

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

输入 PowerShell 脚本后，选择“查看和启动” 

![EC2 查看和启动][aws-ec2configure2]

在查看屏幕上，选择“启动”。   然后，将下拉列表项更改为“在不使用密钥对的情况下继续”，并选中指出你知道密码的复选框。 

![AWS 密钥对选择][aws-keypair]

最后，依次选择“启动实例”、“查看实例”。    现已获得所创建的 Service Fabric 群集的基本信息，接下来需将一些最终配置添加到实例本身，以便为 Service Fabric 配置准备好这些实例。

## <a name="modify-the-security-group"></a>修改安全组

Service Fabric 要求在群集中的主机之间打开一些端口。 若要在 AWS 基础结构中打开这些端口，请选择创建的某个实例。 然后选择安全组的名称，例如 **launch-wizard-1**。 现在，请选择“入站”选项卡。 

为了避免向外界打开这些端口，请只对同一安全组中的主机打开这些端口。 记下安全组 ID（在本示例中为 **sg-c4fb1eba**）。  然后选择“编辑”。 

接下来，在安全组中添加针对服务依赖项的四个规则，并额外添加针对 Service Fabric 本身的三个规则。 第一个规则允许 ICMP 流量，用于执行基本连接性检查。 其他规则打开所需的端口，以启用 SMB 和远程注册表。

对于第一个规则，请选择“添加规则”，然后从下拉菜单中选择“所有 ICMP - IPv4”。   选择自定义规则旁边的输入框，并输入前面记下的安全组 ID。

对于最后三个依赖项，需要遵循类似的过程。  选择“添加规则”，从下拉菜单中选择“自定义 TCP 规则”，并在端口范围中为每个规则输入 `135`、`137-139` 和 `445` 中的一项。 最后，在源框中输入安全组 ID。

![安全组端口][aws-ec2securityports]

打开依赖项的端口后，需要针对 Service Fabric 本身用来通信的端口执行相同的操作。 选择“添加规则”，从下拉菜单中选择“自定义 TCP 规则”，在端口范围中输入 `20001-20031`，并在源框中输入安全组。

接下来，针对临时端口范围添加规则。  选择“添加规则”，从下拉菜单中选择“自定义 TCP 规则”，并在端口范围中输入 `20606-20861`。 最后，在源框中输入安全组 ID。

对于 Service Fabric 的最后两个规则，请对外界打开端口，以便可以在个人电脑中管理 Service Fabric 群集。 选择“添加规则”，从下拉菜单中选择“自定义 TCP 规则”，在端口范围中输入 `19000-19003` 和 `19080-19081` 中的一项，然后将“源”下拉列表项更改为“任意位置”。

最后，只需打开端口 8080，以便在部署应用程序时能够看到它。 选择“添加规则”，从下拉菜单中选择“自定义 TCP 规则”，在端口范围中输入 `8080`，然后将“源”下拉列表项更改为“任意位置”。

现已输入所有规则。 选择“保存”。 

## <a name="connect-to-an-instance-and-validate-connectivity"></a>连接到实例并验证连接

在安全组选项卡上，从左侧菜单中选择“实例”。   选择创建的每个实例并记下其专用 IP 地址。以下示例将使用 `172.31.21.141` 和 `172.31.20.163`。

获取所有 IP 地址后，选择要连接到的某个实例，右键单击该实例并选择“连接”。   在此处可以下载此特定实例的 RDP 文件。  选择“下载远程桌面文件”，然后打开下载的文件，以与此实例建立远程桌面连接 (RDP)。   根据提示输入密码 `serv1ceF@bricP@ssword`。

![下载远程桌面文件][aws-rdp]

成功连接到实例后，验证是否能够在不同的实例之间建立连接，以及是否能够共享文件。  现已收集所有实例的 IP 地址，接下来请选择当前未连接到的 IP 地址。 转到“开始”，输入 `cmd`，并选择“命令提示符”。

在这些示例中，已经与以下 IP 地址建立 RDP 连接：172.31.21.141。 然后，针对另一个 IP 地址执行所有连接测试：172.31.20.163。

若要验证基本连接是否正常，请使用 ping 命令。

```
ping 172.31.20.163
```

如果类似于 `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` 的输出重复出现四次，则表示实例之间的连接正常。  现在，使用以下命令验证 SMB 共享功能是否正常工作：

```
net use * \\172.31.20.163\c$
```

此命令应返回 `Drive Z: is now connected to \\172.31.20.163\c$.` 作为输出。

## <a name="prep-instances-for-service-fabric"></a>准备 Service Fabric 的实例

如果从头开始创建了此项目，则需要执行几个额外的步骤。  即，需要验证远程注册表是否已运行、启用 SMB，并为 SMB 和远程注册表打开所需的端口。

为方便操作，我们在启动实例时已使用用户数据脚本嵌入了所有这些工作。

为了启用 SMB，我们使用了以下 PowerShell 命令：

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

若要在防火墙中打开端口，请使用以下 PowerShell 命令：

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>后续步骤

本系列教程的第一篇文章介绍了如何启动三个 EC2 实例，并将其配置为用于 Service Fabric 安装：

> [!div class="checklist"]
> * 创建一组 EC2 实例
> * 修改安全组
> * 登录到其中一个实例
> * 准备 Service Fabric 的实例

请继续学习本系列教程的第二篇文章，了解如何在群集中配置 Service Fabric。

> [!div class="nextstepaction"]
> [安装 Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png