---
title: 微软防御者高级威胁保护 - Azure 安全中心
description: 本文档介绍了 Azure 安全中心和 Microsoft 防御者高级威胁保护之间的集成。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: a502541a5a01ec3304338054239fc3b50f4d3c61
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810306"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>微软防御者高级威胁防护与 Azure 安全中心

Azure 安全中心正在通过与[Microsoft 防御者高级威胁防护](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp)（ATP） 集成来扩展其云工作负载保护平台产品。
此项更改带来了全面的终结点检测和响应 (EDR) 功能。 通过微软后卫ATP集成，您可以发现异常。 还可以检测和响应 Azure 安全中心所监视的服务器终结点上出现的高级攻击。

## <a name="microsoft-defender-atp-features-in-security-center"></a>微软后卫ATP功能在安全中心

当您使用微软后卫ATP时，您将获得：

- **先进的漏洞后检测传感器**：微软卫士ATP传感器为Windows服务器收集大量的行为信号。

- **基于分析的、云驱动的漏洞检测**：微软防御者ATP快速适应不断变化的威胁。 它使用高级分析和大数据。 Microsoft Defender ATP 被智能安全图的强大功能放大了，该图具有跨 Windows、Azure 和 Office 的信号来检测未知威胁。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁情报**：微软防御者 ATP 在识别攻击者的工具、技术和过程时生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

以下功能现在可在 Azure 安全中心中提供：

- **自动载入**：Microsoft Defender ATP 传感器自动启用已载入 Azure 安全中心的 Windows 服务器（运行 Windows Server 2019 的服务器除外）。

- **单个窗格**：Azure 安全中心控制台显示 Microsoft Defender ATP 警报。

- **详细的机器调查**：Azure 安全中心客户可以使用 Microsoft Defender ATP 控制台进行详细调查，以发现违规范围。

![Azure 安全中心，其中显示了警报列表以及有关每条警报的一般信息](media/security-center-wdatp/image1.png)

要进一步调查，请使用微软后卫ATP。 Microsoft Defender ATP 提供了其他信息，如警报过程树和事件图。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

![微软后卫ATP页面，包含有关警报的详细信息](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支持

安全中心的 Microsoft Defender ATP 支持在 Windows Server 2016、2012 R2 和 2008 R2 SP1 上进行检测，对于需要标准层订阅的 Azure VM，对于仅需要在工作区级别中使用标准层的非 Azure VM。

> [!NOTE]
> 当您使用 Azure 安全中心监视服务器时，将自动创建 Microsoft Defender ATP 租户，默认情况下，Microsoft Defender ATP 数据将存储在欧洲。 如果需要将数据移动到其他位置，则需要联系 Microsoft 支持以重置租户。 已为 Office 365 GCC 客户禁用了使用此集成的服务器终结点监视。

## <a name="onboarding-servers-to-security-center"></a>将服务器加入安全中心 

要将服务器载入安全中心，请单击"**转到 Azure 安全中心"，从**Microsoft Defender ATP 服务器载入板载服务器。

1. 在 **"入职"** 区域中，选择或创建一个工作区，用于存储数据。 <br>
2. 如果无法查看所有工作区，可能是由于权限不足，请确保工作区已设置为 Azure 安全标准层。 有关详细信息，请参阅[升级到安全中心的标准层，以提高安全性](security-center-pricing.md)。
    
3. 选择 **"添加服务器**"以查看有关如何安装日志分析代理的说明。 

4. 入职后，您可以在 **"计算"和"应用程序**"下监视计算机。

   ![加入计算机](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>启用微软后卫ATP集成

要查看是否启用了 Microsoft Defender ATP 集成，请选择**安全中心** > **定价&设置**>单击您的订阅。
在此处可以查看当前已启用的集成。

  ![启用 Microsoft 防御者 ATP 集成的 Azure 安全中心威胁检测设置页面](media/security-center-wdatp/enable-integrations.png)

- 如果已将服务器载入 Azure 安全中心标准层，则不需要执行进一步的操作。 Azure 安全中心将自动将服务器上载到 Microsoft 防御者 ATP。 入职可能需要长达 24 小时。

- 如果你从未将服务器载入 Azure 安全中心标准层，请照常将它们载入 Azure 安全中心。

- 如果您已通过 Microsoft Defender ATP 登上服务器：
  - 请参阅有关[如何卸载服务器计算机](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南文档。
  - 将这些服务器载入 Azure 安全中心。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>访问微软后卫 ATP 门户

遵照[为用户分配门户访问权限](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)中的说明操作。

## <a name="set-the-firewall-configuration"></a>设置防火墙配置

如果您有阻止匿名流量的代理或防火墙，因为 Microsoft Defender ATP 传感器正在从系统上下文进行连接，请确保允许匿名流量。 按照启用[对代理服务器中的 Microsoft Defender ATP 服务 URL](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)的访问中的说明进行操作。

## <a name="test-the-feature"></a>测试功能

要生成良性的 Microsoft 防御者 ATP 测试警报：

1. 创建文件夹"C：\测试-MDATP-测试"。

1. 使用远程桌面访问 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。 打开命令行窗口。

1. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![包含上述命令的命令提示符窗口](media/security-center-wdatp/image4.jpeg)

1. 如果命令成功，您将在 Azure 安全中心仪表板和 Microsoft Defender ATP 门户上看到新的警报。 此警报可能要在几分钟之后才显示。

1. 要查看安全中心的警报，请访问**安全警报** > **可疑电源 Shell 命令行**。

1. 从调查窗口，选择要转到 Microsoft Defender ATP 门户的链接。

## <a name="next-steps"></a>后续步骤

- [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
- [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何配置 Azure 订阅和资源组的安全策略。
- [在 Azure 安全中心管理安全建议](security-center-recommendations.md)：了解安全建议如何帮助保护 Azure 资源。
- [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。