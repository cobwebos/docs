---
title: 虚拟机认证 - 问题和解决方案
description: 本文说明了 VM 映像的常见错误消息。 它还讨论相关解决方案
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: v-miegge
ms.author: v-krmall
ms.date: 06/16/2020
ms.openlocfilehash: e73ff612aa07016f69c717a74678d5d1923e87b8
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120647"
---
# <a name="issues-and-solutions-during-virtual-machine-certification"></a>虚拟机认证过程中的问题和解决方案 

将虚拟机（VM）映像发布到 Azure Marketplace 时，Azure 团队会对其进行验证，以确保其 bootability、安全性和 Azure 兼容性。 如果任何高质量测试失败，则发布将失败，并且你将收到一条描述问题的错误消息。

本文介绍了 VM 映像发布期间的常见错误消息，以及相关解决方案。

> [!NOTE]
> 如果你有疑问或反馈，请联系[合作伙伴中心支持](https://partner.microsoft.com/support/v2/?stage=1)部门。

## <a name="approved-base-image"></a>批准的基本映像

提交请求以重新发布包含更新的映像时，部件号验证测试用例可能会失败。 如果失败，则不会批准映像。

当使用属于另一发布服务器的基本映像并且已更新该映像时，将发生此错误。 在这种情况下，你将不能发布映像。

若要解决此问题，请从 Azure Marketplace 检索映像，并对其进行更改。 有关详细信息，请参阅以下文章：

- [Linux 映像](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows 映像](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

## <a name="vm-extension-failure"></a>VM 扩展失败

查看映像是否支持 VM 扩展。

若要启用 VM 扩展，请执行以下操作：

1. 选择你的 Linux VM。
1. 请参阅 "**诊断设置**"。
1. 通过更新**存储帐户**启用基本矩阵。
1. 选择“保存”。

   ![启用来宾级监视](./media/vm-certification-issues-solutions-1.png)

若要验证是否已正确激活 VM 扩展，请执行以下操作：

1. 在 VM 中，选择 " **vm 扩展**" 选项卡，然后验证**Linux 诊断扩展**的状态。
    * 如果 "状态"*设置*为 "已成功"，则已通过扩展测试用例。  
    * 如果状态为 "*预配失败*"，则扩展测试用例失败，需要设置强化标志。

      ![显示预配已成功的屏幕截图](./media/vm-certification-issues-solutions-2.png)

      如果 VM 扩展失败，请参阅[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)以启用它。 如果你不希望启用 VM 扩展，请联系支持团队，并要求他们禁用该扩展。

## <a name="vm-provisioning-issue"></a>VM 设置问题

在提交产品/服务之前，请检查以确保已遵循 VM 预配过程。 若要查看用于预配 VM 的 JSON 格式，请参阅[Azure 虚拟机映像证书](azure-vm-image-certification.md)。

预配问题可能包括以下故障方案：

|方案|错误|原因|解决方案|
|---|---|---|---|
|1|虚拟硬盘（VHD）无效|如果 VHD 页脚中的指定 cookie 值不正确，则 VHD 将被视为无效。|重新创建映像并提交请求。|
|2|无效的 blob 类型|VM 预配失败，因为使用的块是 blob 类型而不是页类型。|重新创建映像并提交请求。|
|3|预配超时或未正确通用化|VM 通用化出现问题。|用通用化重新创建映像并提交请求。|

> [!NOTE]
> 有关 VM 通用化的详细信息，请参阅：
> - [Linux 文档](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Windows 文档](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>适用于 Windows 的软件符合性

如果由于软件合规性问题而拒绝 Windows 映像请求，则可能是使用已安装的 SQL server 实例创建了 Windows 映像，而不是从 Azure Marketplace 获取相关的 SQL 版本基本映像。

请勿创建你自己的 Windows 映像，并在其中安装 SQL server。 请改用 Azure Marketplace 中已批准的 SQL 基本映像（企业/标准/web）。

如果你正在尝试安装 Visual Studio 或任何 Office 许可产品，请联系支持团队以获得预先批准。

有关选择已批准基准的详细信息，请参阅[创建 Azure 虚拟机技术资产](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)。

## <a name="tool-kit-test-case-execution-failed"></a>工具包测试用例执行失败

Microsoft 认证工具包可帮助你运行测试用例，并验证你的 VHD 或映像是否与 Azure 环境兼容。

下载[Microsoft 认证工具包](azure-vm-image-certification.md)。

## <a name="linux-test-cases"></a>Linux 测试用例

下表列出了工具包将运行的 Linux 测试用例。 说明中说明了测试验证。

|方案|测试用例|说明|
|---|---|---|
|1|Bash 历史记录|在创建 VM 映像之前，应清除 Bash 历史记录文件。|
|2|Linux 代理版本|应安装 Azure Linux 代理2.2.41 或更高版本。|
|3|必需的内核参数|验证是否已设置以下内核参数： <br>控制台 = ttyS0<br>earlyprintk = ttyS0<br>rootdelay=300|
|4|在 OS 磁盘上交换分区|验证是否未在 OS 磁盘上创建交换分区。|
|5|OS 磁盘上的根分区|为 OS 磁盘创建一个根分区。|
|6|OpenSSL 版本|OpenSSL 版本应为 v 0.9.8 或更高版本。|
|7|Python 版本|强烈建议使用 Python 版本2.6 或更高版本。|
|8|客户端活动间隔|将 ClientAliveInterval 设置为180。 在应用程序需要时，可以将其从30设置为235。 如果要为最终用户启用 SSH，则必须按照说明设置此值。|
|9|OS 体系结构|仅支持 64 位操作系统。|
|10|自动更新|确定是否启用 Linux 代理自动更新。|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>执行前面的测试用例时发现常见错误

下表列出了在执行前面的测试用例时发现的常见错误：
 
|方案|测试用例|错误|解决方案|
|---|---|---|---|
|1|Linux 代理版本测试用例|最低 Linux 代理版本为2.241 或更高版本。 此要求是必需的，因为2020年5月1日。|必须用[提交请求](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)所需的版本更新映像。|
|2|Bash 历史记录测试用例|如果提交图像的 bash 历史记录大小超过 1 kb，将会出现错误。 大小限制为 1 KB，以确保不会在 bash 历史记录文件中捕获任何潜在的敏感信息。|若要解决此问题，请将 VHD 装载到任何其他工作 VM，并进行所需的任何更改（例如，删除*bash*历史记录文件），以将大小减小到小于或等于 1 KB。|
|3|必需的内核参数测试用例|如果**console**的值未设置为**ttyS0**，则会收到此错误。 通过运行以下命令进行检查：<br>`cat /proc/cmdline`|将**console**的值设置为**ttyS0**，然后重新提交请求。|
|4|ClientAlive 间隔测试用例|如果该测试用例的工具包结果提供了失败的结果，则**ClientAliveInterval**的值不正确。|将**ClientAliveInterval**的值设置为小于或等于235，然后重新提交请求。|

### <a name="windows-test-cases"></a>Windows 测试用例

下表列出了工具包将运行的 Windows 测试用例，以及测试验证的说明：

|方案 |测试事例|说明|
|---|---|---|---|
|1|OS 体系结构|Azure 仅支持64位操作系统。|
|2|用户帐户依赖项|应用程序的执行不应依赖于管理员帐户。|
|3|故障转移群集|目前尚不支持 Windows Server 故障转移群集功能。 应用程序不应依赖于此功能。|
|4|IPV6|Azure 环境目前尚不支持 IPv6。 应用程序不应依赖于此功能。|
|5|DHCP|目前尚不支持动态主机配置协议服务器角色。 应用程序不应依赖于此功能。|
|6|Hyper-V|目前尚不支持 hyper-v 服务器角色。 应用程序不应依赖于此功能。|
|7|远程访问|目前尚不支持远程访问（直接访问）服务器角色。 应用程序不应依赖于此功能。|
|8|权限管理服务|Rights Management 服务。 尚不支持该服务器角色。 应用程序不应依赖于此功能。|
|9|Windows 部署服务|Windows 部署服务。 尚不支持该服务器角色。 应用程序不应依赖于此功能。|
|10|BitLocker 驱动器加密|操作系统不受操作系统硬盘支持，但它可以在数据磁盘上使用。 BitLocker 驱动器加密|
|11|Internet 存储名称服务器|目前尚不支持 Internet 存储名称服务器功能。 应用程序不应依赖于此功能。|
|12|多路径 I/O|多路径 i/o。 此服务器功能尚不受支持。 应用程序不应依赖于此功能。|
|13|Network Load Balancing|网络负载平衡。 此服务器功能尚不受支持。 应用程序不应依赖于此功能。|
|14|对等名称解析协议|对等名称解析协议。 此服务器功能尚不受支持。 应用程序不应依赖于此功能。|
|15|SNMP 服务|尚不支持简单网络管理协议（SNMP）服务功能。 应用程序不应依赖于此功能。|
|16|Windows Internet 名称服务|Windows Internet 名称服务。 此服务器功能尚不受支持。 应用程序不应依赖于此功能。|
|17|无线 LAN 服务|无线 LAN 服务。 此服务器功能尚不受支持。 应用程序不应依赖于此功能。|

如果前面的测试用例遇到任何故障，请参阅该解决方案的表中的 "**说明**" 列。 如果需要详细信息，请与支持团队联系。 

## <a name="data-disk-size-verification"></a>数据磁盘大小验证

如果随数据磁盘提交的请求的大小大于 1023 gb，则不会批准该请求。 此规则适用于 Linux 和 Windows。

重新提交大小小于或等于 1023 GB 的请求。

## <a name="os-disk-size-validation"></a>OS 磁盘大小验证

有关操作系统磁盘大小的限制，请参阅以下规则。 提交任何请求时，验证 OS 磁盘大小是否在 Linux 或 Windows 的限制范围内。

|(OS)|推荐的 VHD 大小|
|---|---|
|Linux|30 GB 到 1023 GB|
|Windows|30 GB 到 250 GB|

虚拟机允许访问基础操作系统时，请确保 VHD 大小对于 VHD 而言足够大。 由于磁盘无法在无停机时间的情况下扩展，因此请使用磁盘大小从 30 GB 到 50 GB。

|VHD 大小|实际占用的大小|解决方案|
|---|---|---|
|>500 tib （TiB）|不适用|请联系支持团队以获取异常批准。|
|250-500 TiB|>200 gb （GiB）与 blob 大小的差异|请联系支持团队以获取异常批准。|

> [!NOTE]
> 较大的磁盘大小会产生较高的成本，并将在安装和复制过程中产生延迟。 由于此延迟和成本，支持团队可能会寻找异常批准的理由。

## <a name="wannacry-patch-verification-test-for-windows"></a>适用于 Windows 的 WannaCry 修补程序验证测试

若要防止与 WannaCry 病毒相关的潜在攻击，请确保使用最新修补程序更新所有 Windows 映像请求。

若要查看 Windows Server 修补版本的操作系统详细信息及其支持的最低版本，请参阅下表： 

可以通过或验证图像文件版本 `C:\windows\system32\drivers\srv.sys` `srv2.sys` 。

> [!NOTE]
> Windows Server 2019 没有任何必需的版本要求。

|(OS)|Version|
|---|---|
|Windows 服务 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>SACK 漏洞修补程序验证

提交 Linux 映像时，可能会因为内核版本问题而拒绝请求。

使用已批准的版本更新内核，并重新提交请求。 可以在下表中找到已批准的内核版本。 版本号应等于或大于此处列出的编号。

如果映像未与以下某个内核版本一起安装，请将其更新为正确的修补程序。 在用以下必需的修补程序更新映像后，请求支持团队提供必要的批准：

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|OS 系列|版本|内核|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04 |5.0.0-1010|
||19.04 |5.3.0-1004|
|RHEL 和美分 OS|6.10|2.6.32-754.15。3|
||7.2|3.10.0-327.79。2|
||7.3|3.10.0-514.66。2|
||7.4|3.10.0-693.50。3|
||7.5|3.10.0-862.34。2|
||7.6|3.10.0-957.21。3|
||7.7|3.10.0-1062.1。1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-原始" （7.6）||
||"7-LVM" （7.6）|3.10.0-957.21。3|
||RHEL-SAP 7。4|TBD|
||RHEL-SAP 7。5|TBD|
|SLES|SLES11SP4 （包括 SAP）|3.0.101-108.95.2|
||适用于 SAP 的 SLES12SP1|3.12.74-60.64.115.1|
||适用于 SAP 的 SLES12SP2|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 （内核-azure）|
||适用于 SAP 的 SLES12SP3|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 （内核-azure）|
||适用于 SAP 的 SLES12SP4|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 （内核-azure）|
||适用于 SAP 的 SLES15|4.12.14-5.30.1 （内核-azure）|
||SLES15SP1|4.12.14-5.30.1 （内核-azure）|
|Oracle|6.10|UEK2 2.6.39-400.312。2<br>UEK3 3.8.13-118.35。2<br>RHCK 2.6.32-754.15。3 
||7.0-7。5|UEK3 3.8.13-118.35。2<br>UEK4 4.1.12-124.28。3<br>RHCK 遵循 RHEL|
||7.6|RHCK 3.10.0-957.21。3<br>UEK5 4.14.35-1902.2.0|
|CoreOS 稳定2079.6。0|4.19.43*|
||Beta 2135.3。1|4.19.50*|
||Alpha 2163.2。1|4.19.50*|
|Debian|jessie （安全）|3.16.68-2|
||jessie precise-backports|4.9.168 + deb9u3|
||stretch （安全）|4.9.168 + deb9u3|
||Debian GNU/Linux 10 （buster）|Debian 6.3.0-18 + deb9u1|
||buster、sid （stretch precise-backports）|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>图像大小应为兆字节的倍数

Azure 上的所有 Vhd 都必须将虚拟大小调整为 1 mb 的倍数。 如果 VHD 不符合建议的虚拟大小，则可能会拒绝你的请求。

在将原始磁盘转换为 VHD 时遵循指导原则，并确保原始磁盘大小为 1 MB 的倍数。 有关详细信息，请参阅有关[非认可分发的信息](../../virtual-machines/linux/create-upload-generic.md)。

## <a name="vm-access-denied"></a>拒绝访问 VM

如果在 VM 上运行测试用例时遇到访问被拒绝问题，可能是因为没有足够的权限来运行测试用例。

检查是否为运行自测事例的帐户启用了正确的访问权限。 如果未启用访问权限，请启用它以运行测试用例。 如果你不想启用访问，则可能会与支持团队共享自测用例结果。

## <a name="download-failure"></a>下载失败
    
请参阅下表，了解当你使用共享访问签名（SAS） URL 下载 VM 映像时出现的任何问题。

|方案|错误|原因|解决方案|
|---|---|---|---|
|1|找不到 Blob|VHD 可以从指定位置删除或移动。|| 
|2|Blob 正在使用中|VHD 由其他内部进程使用。|使用 SAS URL 下载 VHD 时，VHD 应处于已使用状态。|
|3|无效 SAS URL|VHD 的关联 SAS URL 不正确。|获取正确的 SAS URL。|
|4|签名无效|VHD 的关联 SAS URL 不正确。|获取正确的 SAS URL。|
|6|HTTP 条件头|SAS URL 无效。|获取正确的 SAS URL。|
|7|VHD 名称无效|检查以确定是否有任何特殊字符，如百分号（%）VHD 名称中有引号（"）。|通过删除特殊字符来重命名 VHD 文件。|

## <a name="first-1-mb-partition"></a>前 1 MB 分区

提交 VHD 时，请确保 VHD 的第一个 1 MB 分区为空。 否则，你的请求将失败。

## <a name="default-credentials"></a>默认凭据

请始终确保不会随提交的 VHD 一起发送默认凭据。 添加默认凭据会使 VHD 更容易受到安全威胁。 请改为在提交 VHD 时创建自己的凭据。
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk 映射不正确

当使用多个数据磁盘提交请求，但未按顺序排列时，这会被视为映射问题。 例如，如果有三个数据磁盘，则编号顺序必须是*0、1、2*。 任何其他订单都被视为一个映射问题。

请重新提交请求，并对数据磁盘进行适当的排序。

## <a name="incorrect-os-mapping"></a>OS 映射不正确

创建映像时，它可能映射到或分配了错误的 OS 标签。 例如，当你在创建映像时选择 " **Windows** " 作为 OS 名称的一部分时，os 磁盘应仅与 windows 一起安装。 同一要求适用于 Linux。

## <a name="vm-not-generalized"></a>VM 未通用化

如果要重复使用从 Azure Marketplace 获取的所有映像，则必须通用化操作系统 VHD。

* 对于**linux**，以下过程通用化 linux vm，并将其重新部署为单独的 vm。

  在 SSH 窗口中，输入以下命令：`sudo waagent -deprovision+user`

* 对于**windows**，你使用来通用化 windows 映像 `sysreptool` 。

有关此工具的详细信息，请参阅[系统准备（Sysprep）概述]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

## <a name="datadisk-errors"></a>DataDisk 错误

有关与数据磁盘相关的错误的解决方案，请使用下表：

|错误|原因|解决方案|
|---|---|---|
|`DataDisk- InvalidUrl:`|如果提交产品/服务时为逻辑单元号（LUN）指定了无效的数字，则可能会发生此错误。|验证数据磁盘的 LUN 编号序列是否位于 "合作伙伴中心" 中。|
|`DataDisk- NotFound:`|出现此错误的原因可能是数据磁盘未位于指定的 SAS URL。|验证数据磁盘是否位于请求中指定的 SAS URL。|

## <a name="remote-access-issue"></a>远程访问问题

如果没有为 Windows 映像启用远程桌面协议（RDP）选项，您将收到此错误。 

提交 Windows 映像之前对其启用 RDP 访问。

## <a name="next-steps"></a>后续步骤

如果你有疑问或反馈，请联系[合作伙伴中心支持](https://partner.microsoft.com/support/v2/?stage=1)部门。
