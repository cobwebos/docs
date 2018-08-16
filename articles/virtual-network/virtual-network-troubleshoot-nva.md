---
title: Azure 中网络虚拟设备问题故障排除 | Microsoft Docs
description: 了解如何对 Azure 中的网络虚拟设备问题进行故障排除。
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: genli
ms.openlocfilehash: 5f11129e562ec47f4c81790cf21c9a390cc697b8
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621399"
---
#  <a name="network-virtual-appliance-issues-in-azure"></a>Azure 中的网络虚拟设备问题

在 Microsoft Azure 中使用第三方网络虚拟设备 (NVA) 时，可能会遇到 VM 或 VPN 连接问题和错误。 本文介绍了帮助你验证适用于 NVA 配置的基本 Azure 平台要求的基本步骤。

NVA 供应商提供了对第三方 NVA 及其与 Azure 平台集成的技术支持。 如果遇到涉及 NVA 的连接或路由问题，则应直接[联系 NVA 的供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>与 NVA 供应商合作进行故障排除时的清单

- NVA VM 软件的软件更新
- 服务帐户设置和功能
- 虚拟网络子网上用户定义的路由 (UDR)，用于将流量定向到 NVA
- 虚拟网络子网上的 UDR，用于定向来自 NVA 的流量
- NVA 内的路由表和规则（例如，从 NIC1 到 NIC2）
- 跟踪 NVA NIC 以验证接收和发送网络流量

## <a name="basic-troubleshooting-steps"></a>基本故障排除步骤

- 检查基本配置
- 检查 NVA 性能
- 高级网络故障排除

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>检查 Azure 上 NVA 的最低配置要求

每个 NVA 都满足在 Azure 上正常运行的基本配置要求。 以下部分提供了验证这些基本配置的步骤。 有关详细信息，请[联系 NVA 的供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

**检查 NVA 是否启用了 IP 转发**

使用 Azure 门户

1.  在 [Azure 门户](https://portal.azure.com)中找到 NVA 资源，选择“网络”，然后选择“网络接口”。
2.  在“网络接口”页上，选择“IP 配置”。
3.  确保已启用 IP 转发。

使用 PowerShell

1. 打开 PowerShell 并登录到 Azure 帐户。
2. 运行以下命令（用你的信息替换括号中的值）：

        Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. 检查“EnableIPForwarding”属性。
 
4. 如果未启用 IP 转发，请运行以下命令将其启用：

          $nic2 = Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
          $nic2.EnableIPForwarding = 1
          Set-AzureRmNetworkInterface -NetworkInterface $nic2
          Execute: $nic2 #and check for an expected output:
          EnableIPForwarding   : True
          NetworkSecurityGroup : null

**检查流量是否可路由到 NVA**

1. 在 [Azure 门户](https://portal.azure.com)上，打开“网络观察程序”，选择“下一跃点”。
2. 指定要作为下一跃点的 NVA 的 VM，以及用于查看下一跃点的目标 IP 地址。 
3. 如果 NVA 未列为“下一跃点”，请检查并更新 Azure 路由表。

**检查流量是否可到达 NVA**

1.  在 [Azure 门户](https://portal.azure.com)中，打开“网络观察程序”，然后选择“IP 流验证”。 
2.  指定 VM 和 NVA 的 IP 地址，然后检查是否有任何网络安全组 (NSG) 阻止该流量。
3.  如果存在阻止流量的 NSG 规则，请在“有效安全”规则中找到 NSG，并更新它以允许流量通过。 然后再次运行“IP 流验证”并使用“连接性检查”测试从 VM 到内部或外部 IP 地址的 TCP 通信。

**检查 NVA 和 VM 是否正在侦听预期的流量**

1.  使用 RDP 或 SSH 连接到 NVA，然后运行以下命令：

    对于 Windows：

        netstat -an

    对于 Linux：

        netstat -an | grep -i listen
2.  如果未看到结果中列出的 NVA 软件使用的 TCP 端口，则必须在 NVA 和 VM 上配置应用程序，以侦听并响应到达这些端口的流量。 [如有需要，请联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

## <a name="check-nva-performance"></a>检查 NVA 性能

### <a name="validate-vm-cpu"></a>验证 VM CPU

如果 CPU 使用率接近 100%，则可能会遇到造成网络数据包丢失的问题。 VM 报告 Azure 门户中特定时间跨度的平均 CPU。 在 CPU 峰值期间，调查来宾 VM 上的哪个进程导致高 CPU 使用率，并在可能的情况下缓解该问题。 可能还必须将 VM 大小调整为更大的 SKU 大小；或者，对于虚拟机规模集，可增加实例数或设置为自动调整 CPU 使用率。 对于上述任意问题，如有需要，[请联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="validate-vm-network-statistics"></a>验证 VM 网络统计信息 

如果遇到 VM 网络使用高峰或显示高使用率的时段，可能还必须增加 VM 的 SKU 大小以获得更高的吞吐量容量。 还可以通过启用加速网络来重新部署 VM。 若要验证 NVA 是否支持加速网络功能，如有需要，[请联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

## <a name="advanced-network-administrator-troubleshooting"></a>高级网络管理员故障排除

### <a name="capture-network-trace"></a>捕获网络跟踪
运行 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) 或 Nmap 时，请在源 VM、NVA 和目标 VM 上捕获同步网络跟踪，并停止跟踪。

1. 若要捕获同步网络跟踪，请运行以下命令：

    对于 Windows：

        netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

    对于 Linux：

        sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. 使用从源 VM 到目标 VM 的 PsPing 或 Nmap（例如：`PsPing 10.0.0.4:80` 或 `Nmap -p 80 10.0.0.4`）。

3. 使用[网络监视器](https://www.microsoft.com/download/details.aspx?id=4865)或 tcpdump 从目标 VM 打开网络跟踪。 为运行 PsPing 或 Nmap 的源 VM 的 IP 应用显示筛选器，例如 `IPv4.address==10.0.0.4 (Windows netmon)` 或 `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux)。

### <a name="analyze-traces"></a>分析跟踪

如果无法看到数据包传入到后端 VM 跟踪，原因很可能是存在 NSG 或 UDR 干扰或是 NVA 路由表不正确。

如果看到数据包传入但没有响应，则可能需要解决 VM 应用程序或防火墙问题。 对于上述任意问题，如有需要[请联系 NVA 供应商以获取帮助](Let's link customer to 3rd party vendor again here: https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

