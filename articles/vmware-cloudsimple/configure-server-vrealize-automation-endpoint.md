---
title: Azure VMware 解决方案 (按 CloudSimple)-设置私有云上的 vCenter 以实现 vRealize 自动化
description: 介绍如何在 CloudSimple 私有云上将 VMware vCenter 服务器设置为 VMware vRealize 自动化的终结点
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642391"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>在私有云上为 VMware vRealize Automation 设置 vCenter

可以将 CloudSimple 私有云上的 VMware vCenter 服务器设置为 VMware vRealize 自动化的终结点。

## <a name="before-you-begin"></a>开始之前

在配置 vCenter 服务器之前完成以下任务:

* 配置本地环境和私有云之间的[站点到站点 VPN 连接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* [将本地 dns 请求的 dns 转发](on-premises-dns-setup.md)到私有云的 dns 服务器。
* 提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), 使用下表中列出的权限集创建 VRealize Automation IaaS 管理用户。

| 属性值 | 权限 |
------------ | ------------- |  
| 数据存储 |  分配空间 <br> 浏览数据存储 |
| 数据存储群集 | 配置数据存储群集 |
| 文件夹 | 创建文件夹 <br>删除文件夹 |
| 全局 |  管理自定义属性<br>设置自定义属性 |
| 网络 | 分配网络 |
| 权限 | 修改权限 |
| Resource | 将 VM 分配到资源池<br>迁移关闭的虚拟机<br>迁移已启动的虚拟机 |
| 虚拟机清单 |  从现有创建<br>新建<br>移动<br>Remove | 
| 虚拟机交互 |  配置 CD 媒体<br>控制台交互<br>设备连接<br>关机<br>开机<br>重置<br>挂起<br>工具安装 | 
| 虚拟机配置 |  添加现有磁盘<br>添加新磁盘<br>添加或删除<br>删除磁盘<br>高级<br>更改 CPU 计数<br>更改资源<br>扩展虚拟磁盘<br>磁盘更改跟踪<br>内存<br>修改设备设置<br>重命名<br>设置批注 (版本5.0 及更高版本)<br>设置<br>交换文件位置 |
| 预配 |  自定义<br>克隆模板<br>克隆虚拟机<br>部署模板<br>读取自定义规范 |
| 虚拟机状态 | 创建快照<br>删除快照<br>还原到快照 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>在本地环境中安装 vRealize 自动化

1. 以 CloudSimple 支持为你创建的 IaaS 管理员身份登录到 vRealize Automation IaaS 服务器设备。
2. 为 vRealize 自动化终结点部署 vSphere 代理。
    1. 中转到 https://*vra-url*: 5480/installer, 其中*vra*是用于访问 vRealize 自动化管理 UI 的 url。
    2. 单击**IaaS 安装**程序以下载安装程序。<br>
    安装程序文件的命名约定为 setup_*vra-url*@5480.exe。
    3. 运行安装程序。 在欢迎屏幕上, 单击 "**下一步**"。
    4. 接受 EULA, 并单击 "**下一步**"。
    5. 提供登录信息, 单击 "**接受证书**", 然后单击 "**下一步**"。
    ![vRA 凭据](media/configure-vra-endpoint-login.png)
    6. 选择 "**自定义安装**和**代理**代理" 并单击 "**下一步**"。
    ![vRA 安装类型](media/configure-vra-endpoint-install-type.png)
    7. 输入 IaaS 服务器登录信息, 然后单击 "**下一步**"。 如果使用 Active Directory, 请以**domain\user**格式输入用户名。 否则, 使用 **user@domain** 格式。
    ![vRA 登录信息](media/configure-vra-endpoint-account.png)
    8. 对于 "代理设置", 输入**vSphere**作为**代理类型**。 输入代理名称。
    9. 在**管理器服务主机**和 "**模型管理器 Web 服务主机**" 字段中输入 IaaS 服务器 FQDN。 单击 "**测试**" 以测试每个 FQDN 值的连接。 如果测试失败, 请修改你的 DNS 设置, 以便解析 IaaS 服务器主机名。
    10. 为私有云输入 vCenter 服务器终结点的名称。 记录名称以供稍后在配置过程中使用。

        ![vRA 安装代理](media/configure-vra-endpoint-proxy.png)

    11. 单击“下一步”。
    12. 单击“安装”。

## <a name="configure-the-vsphere-agent"></a>配置 vSphere 代理

1. 请参阅 https://*vra-url*/vcac 并以**ConfigurationAdmin**身份登录。
2. 选择**基础结构** > **终结** > **点**。
3. 选择 "**新建** > **虚拟** > **vSphere**"。
4. 输入在前面的过程中指定的 vSphere 终结点名称。
5. 对于 "**地址**", 以 https://*vcenter-fqdn*/Sdk 格式输入私有云 vCenter Server URL, 其中*vcenter-fqdn*是 vcenter 服务器的名称。
6. 输入 CloudSimple 支持为你创建的 vRealize Automation IaaS 管理用户的凭据。
7. 单击 "**测试连接**" 以验证用户凭据。 如果测试失败, 请验证 URL、帐户信息和[终结点名称](#verify-the-endpoint-name), 然后再次测试。
8. 成功测试后, 单击 **"确定"** 以创建 vSphere 终结点。
    ![vRA 终结点配置访问](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>验证端点名称

若要标识正确的 vCenter 服务器终结点名称, 请执行以下操作:

1. 在 IaaS 设备上打开命令提示符。
2. 将目录更改为 C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, 其中*代理名称*是分配给 vCenter 服务器终结点的名称。
3. 运行以下命令。

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

输出如下所示。 该`managementEndpointName`字段的值为终结点名称。

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
