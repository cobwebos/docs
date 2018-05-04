---
title: 使用动态 DNS 在 Azure 中注册主机名 | Microsoft Docs
description: 了解如何设置动态 DNS，以便在自己的 DNS 服务器中注册主机名。
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: bbbce45b7c321fd4934374c76f2a4421b125d46f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>使用动态 DNS 在自己的 DNS 服务器中注册主机名

[Azure 为虚拟机 (VM) 和角色实例提供名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 如果名称解析需求超出 Azure 的默认 DNS 所提供的功能，则可以提供自己的 DNS 服务器。 使用自己的 DNS 服务器即可量身定制 DNS 解决方案以满足自己的特定需求。 例如，可能需要通过 Active Directory 域控制器来访问本地资源。

将自定义 DNS 服务器作为 Azure VM 托管时，可将针对同一虚拟网络的主机名查询转发到 Azure 以解析主机名。 如果不希望使用此选项，可使用动态 DNS (DDNS) 在 DNS 服务器中注册 VM 主机名。 Azure 不具备直接在 DNS 服务器中创建记录所需的凭据，因此通常需要替代方案。 以下是一些常见的具有替代方案的情况：

## <a name="windows-clients"></a>Windows 客户端
在启动时或其 IP 地址更改时，未加入域的 Windows 客户端会尝试不安全的 DDNS 更新。 DNS 名称为主机名加上的主 DNS 后缀。 Azure 保留主 DNS 后缀为空，但你可以通过[用户界面](https://technet.microsoft.com/library/cc794784.aspx)或 [PowerShell](/powershell/module/dnsclient/set-dnsclient) 在 VM 中对后缀进行设置。

已加入域的 Windows 客户端通过使用安全的 DDNS 将其 IP 地址注册到域控制器。 域加入过程会在客户端上设置主 DNS 后缀并创建和维护信任关系。

## <a name="linux-clients"></a>Linux 客户端
Linux 客户端通常在启动时注册到 DNS 服务器，并假设 DHCP 服务器将执行此操作。 Azure 的 DHCP 服务器没有在 DNS 服务器中注册记录所需的凭据。 可以使用名为 `nsupdate` 的工具发送 DDNS 更新，该工具包含在绑定包中。 由于 DDNS 协议是标准化的，所以即使在 DNS 服务器上未使用绑定，也可以使用 `nsupdate`。

可以使用 DHCP 客户端提供的挂钩在 DNS 服务器中创建和维护主机名条目。 在 DHCP 周期中，客户端将执行 */etc/dhcp/dhclient-exit-hooks.d/* 中的脚本。 可以使用挂钩通过 `nsupdate` 来注册新的 IP 地址。 例如：

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

还可以使用 `nsupdate` 命令来执行安全的 DDNS 更新。 例如，在使用 Bind DNS 服务器时，将[生成](http://linux.yyz.us/nsupdate/)公钥-私钥对。 已为 DNS 服务器[配置](http://linux.yyz.us/dns/ddns-server.html)密钥的公共部分，所以它可以验证请求中的签名。 若要为 `nsupdate` 提供密钥对，请使用 `-k` 选项，以便对 DDNS 更新请求进行签名。

如果使用 Windows DNS 服务器，可以使用 `nsupdate` 中包含 `-g` 参数的 Kerberos 身份验证，但它在 `nsupdate` 的 Windows 版本中不可用。 若要使用 Kerberos，请使用 `kinit` 来加载凭据。 例如，可以从 [keytab 文件](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)加载凭据，然后 `nsupdate -g` 就可以从缓存拾取该凭据。

如果需要，可以将 DNS 搜索后缀添加到 VM。 DNS 后缀在 */etc/resolv.conf* 文件中指定。 大多数 Linux 发行版自动管理此文件中的内容，因此通常不能对其进行编辑。 但是，可以通过使用 DHCP 客户端的 `supersede` 命令重写该后缀。 若要重写该后缀，请将以下行添加到 */etc/dhcp/dhclient.conf* 文件：

```
supersede domain-name <required-dns-suffix>;
```
