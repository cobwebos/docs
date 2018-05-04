---
title: 排查 Azure 中的 Windows 虚拟机激活问题 | Microsoft Docs
description: 介绍了用于修复 Azure 中的 Windows 虚拟机激活问题的疑难解答步骤
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 3926522c8359587798ea540d13b157832551f24d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>排查 Azure Windows 虚拟机激活问题

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

如果无法激活通过自定义映像创建的 Azure Windows 虚拟机 (VM)，可以参照本文档中介绍的信息来排查此问题。 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>了解用于对 Azure 虚拟机进行 Windows 产品激活的 Azure KMS 终结点
Azure 使用不同的终结点进行 KMS 激活，具体取决于 VM 所在的云区域。 使用本故障排除指南时，请使用适用于你所在区域的相应 KMS 终结点。

* Azure 公有云区域：kms.core.windows.net:1688
* Azure 中国国家云区域：kms.core.chinacloudapi.cn:1688
* Azure 德国国家云区域：kms.core.cloudapi.de:1688
* Azure US Gov 国家云区域：kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>症状

尝试激活 Azure Windows VM 时，将看到类似于以下示例的错误消息：

**错误: 0xC004F074 软件授权服务报告无法激活计算机。无法联系任何密钥管理服务(KMS)。有关其他信息，请参阅应用程序事件日志。**

## <a name="cause"></a>原因
通常情况下，如果未使用相应的 KMS 客户端安装密钥配置 Windows VM，或 Windows VM 与 Azure KMS 服务（kms.core.windows.net，端口 1668）的连接出现问题，便会出现 Azure VM 激活问题。 

## <a name="solution"></a>解决方案

>[!NOTE]
>如果使用的是站点间 VPN 和强制隧道，请参阅[使用 Azure 自定义路由通过强制隧道启用 KMS 激活](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx)。 
>
>如果使用的是 ExpressRoute 且已发布默认路由，请参阅 [Azure VM 可能无法通过 ExpressRoute 激活](https://blogs.technet.microsoft.com/jpaztech/2016/05/16/azure-vm-may-fail-to-activate-over-expressroute/)。

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>第 1 步：配置相应的 KMS 客户端安装密钥（对于 Windows Server 2016 和 Windows Server 2012 R2）

对于通过 Windows Server 2016 或 Windows Server 2012 R2 自定义映像创建的 VM，必须为 VM 配置相应的 KMS 客户端安装密钥。

这一步不适用于 Windows 2012 或 Windows 2008 R2。 因为使用的自动虚拟机激活 (AVMA) 功能仅受 Windows Server 2016 和 Windows Server 2012 R2 支持。

1. 在提升的命令提示符处，运行 slmgr.vbs /dlv。 检查输出中的 Description 值，并确定是通过零售 (RETAIL channel) 还是通过卷 (VOLUME_KMSCLIENT) 许可证介质创建的：
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. 如果 slmgr.vbs /dlv 显示 RETAIL channel，运行以下命令，以设置适用于所用 Windows Server 版本的 [KMS 客户端安装密钥](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396)，并强制重试激活操作： 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    例如，对于 Windows Server 2016 数据中心，运行以下命令：

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>第 2 步：验证 VM 与 Azure KMS 服务的连接

1. 将 [Psping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) 工具下载并提取到未激活的 VM 中的本地文件夹。 

2. 转到“开始”，搜索 Windows PowerShell，右键单击 Windows PowerShell，再选择“以管理员身份运行”。

3. 请确保 VM 已配置为使用正确的 Azure KMS 服务器。 为此，请运行以下命令：
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    此命令应返回：密钥管理服务计算机名称已成功设置为 kms.core.windows.net:1688。

4. 使用 Psping 验证是否已连接到 KMS 服务器。 切换到将 Pstools.zip 下载内容提取到的文件夹，再运行以下命令：
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  请确保输出的倒数第二行显示：Sent = 4, Received = 4, Lost = 0 (0% loss)。

  如果“Lost”大于 0（零），表示 VM 未连接到 KMS 服务器。 在这种情况下，如果 VM 位于虚拟网络中，并且指定了自定义 DNS 服务器，必须确保此 DNS 服务器能够解析 kms.core.windows.net。 或者，将 DNS 服务器更改为可以解析 kms.core.windows.net。

  请注意，如果从虚拟网络中删除所有 DNS 服务器，VM 将使用 Azure 的内部 DNS 服务。 此服务可以解析 kms.core.windows.net。
  
此外，还要验证来宾防火墙是否未配置为阻止激活尝试。

5. 验证成功连接到 kms.core.windows.net 后，在提升的 Windows PowerShell 提示符处运行以下命令。 此命令可多次尝试激活。

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

如果激活成功，将返回如下信息：

**正在激活 Windows(R)，已成功激活服务器数据中心版本(12345678-1234-1234-1234-12345678) … 产品。**

## <a name="faq"></a>常见问题 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>我从 Azure Marketplace 创建了 Windows Server 2016。 是否需要配置用于激活 Windows Server 2016 的 KMS 密钥？ 
 
不会。 Azure Marketplace 中的映像已配置了相应的 KMS 客户端安装密钥。 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>无论 VM 是否使用 Azure 混合使用权益 (HUB)，Windows 激活的工作方式是否都一样？ 
 
是的。 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>如果 Windows 激活已过期，会出现什么情况？ 
 
如果宽限期已过期且 Windows 仍未激活，Windows Server 2008 R2 及更高版本的 Windows 将显示有关激活的其他通知。 桌面壁纸将保持黑色不变，并且 Windows 更新将仅安装安全更新程序和关键更新，而不安装可选更新。 请参阅[授权条件](http://technet.microsoft.com/library/ff793403.aspx)页底部的“通知”部分。   

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
