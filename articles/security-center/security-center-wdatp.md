---
title: Microsoft Defender 高级威胁防护-Azure 安全中心
description: 本文档介绍 Azure 安全中心与 Microsoft Defender 高级威胁防护之间的集成。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 46b9fe5c6a038aa98cf8df64c40bf8ea1747efec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663611"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Azure 安全中心的 Microsoft Defender 高级威胁防护

Azure 安全中心通过与[Microsoft Defender 高级威胁防护](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp)（ATP）集成，扩展其云工作负荷保护平台。
此项更改带来了全面的终结点检测和响应 (EDR) 功能。 利用 Microsoft Defender ATP 集成，你可以发现异常。 还可以检测和响应 Azure 安全中心所监视的服务器终结点上出现的高级攻击。

## <a name="microsoft-defender-atp-features-in-security-center"></a>安全中心的 Microsoft Defender ATP 功能

使用 Microsoft Defender ATP 时，将获得：

- **高级入侵后检测传感器**：用于 Windows server 的 MICROSOFT Defender ATP 传感器收集大量行为信号。

- **基于分析、云驱动的入侵后检测**： MICROSOFT Defender ATP 快速适应不断变化的威胁。 它使用高级分析和大数据。 Microsoft Defender ATP 通过使用跨 Windows、Azure 和 Office 的信号来检测未知威胁，从而增强了 Intelligent Security Graph 的强大功能。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁情报**： MICROSOFT Defender ATP 在识别攻击者的工具、技术和过程时生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

Azure 安全中心现在提供了以下功能：

- **自动载入**：对于载入到 Azure 安全中心的 Windows 服务器，会自动启用 MICROSOFT Defender ATP 传感器。

- **单一界面**： Azure 安全中心控制台显示 MICROSOFT Defender ATP 警报。

- **详细的机器调查**： Azure 安全中心客户可以使用 MICROSOFT Defender ATP 控制台进行详细的调查，以找出违规的范围。

![Azure 安全中心，其中显示了警报列表以及有关每条警报的一般信息](media/security-center-wdatp/image1.png)

若要进一步调查，请使用 Microsoft Defender ATP。 Microsoft Defender ATP 提供其他信息，例如警报流程树和事件图。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

![包含警报详细信息的 Microsoft Defender ATP 页面](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支持

安全中心的 Microsoft Defender ATP 支持在 Windows Server 2016、2012 R2 和 2008 R2 SP1 上检测：对于 Azure Vm，你需要标准层订阅，对于非 Azure Vm，你需要仅在工作区级别需要标准层。

> [!NOTE]
> 使用 Azure 安全中心监视服务器时，将自动创建 Microsoft Defender ATP 租户，并且默认情况下，Microsoft Defender ATP 数据存储在欧洲。 如果需要将数据移动到另一个位置，则需要联系 Microsoft 支持部门重置租户。 已为 Office 365 GCC 客户禁用了利用此集成的服务器终结点监视。

## <a name="onboarding-servers-to-security-center"></a>将服务器加入安全中心 

若要将服务器载入到安全中心，请单击 "**前往 Azure 安全中心" 以**从 MICROSOFT Defender ATP 服务器载入中载入服务器。

1. 在 "**载入**" 区域中，选择或创建用于存储数据的工作区。 <br>
2. 如果看不到所有工作区，可能是由于缺少权限，请确保已将工作区设置到“Azure 安全标准”层。 有关详细信息，请参阅[升级到安全中心的标准层以增强安全性](security-center-pricing.md)。
    
3. 选择“添加服务器”以查看有关如何安装 Microsoft Monitoring Agent 的说明。 

4. 加入后，可以在“计算和应用”下监视计算机。

   ![加入计算机](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>启用 Microsoft Defender ATP 集成

若要查看是否已启用 Microsoft Defender ATP 集成，请选择 "**安全中心** > **定价 & 设置**" > 单击订阅。
在此处可以查看当前已启用的集成。

  ![启用了 Microsoft Defender ATP 集成的 Azure 安全中心威胁检测设置页面](media/security-center-wdatp/enable-integrations.png)

- 如果已将服务器载入 Azure 安全中心标准层，则不需要执行进一步的操作。 Azure 安全中心会将服务器自动载入 Microsoft Defender ATP。 载入可能需要长达24小时。

- 如果你从未将服务器载入 Azure 安全中心标准层，请照常将它们载入 Azure 安全中心。

- 如果已通过 Microsoft Defender ATP 载入服务器：
  - 请参阅有关[如何卸载服务器计算机](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南文档。
  - 将这些服务器载入 Azure 安全中心。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>访问 Microsoft Defender ATP 门户

遵照[为用户分配门户访问权限](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)中的说明操作。

## <a name="set-the-firewall-configuration"></a>设置防火墙配置

如果你的代理或防火墙正在阻止匿名流量，因为 Microsoft Defender ATP 传感器从系统上下文连接，请确保允许匿名流量。 按照在[代理服务器中启用对 Microsoft DEFENDER ATP 服务 url 的访问](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的说明进行操作。

## <a name="test-the-feature"></a>测试功能

生成良性的 Microsoft Defender ATP 测试警报：

1. 使用远程桌面访问 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。 打开命令提示符窗口。

2. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![包含上述命令的命令提示符窗口](media/security-center-wdatp/image4.jpeg)

3. 如果此命令成功，你将在 Azure 安全中心仪表板和 Microsoft Defender ATP 门户上看到新警报。 此警报可能要在几分钟之后才显示。

4. 若要在安全中心查看该警报，请转到“安全警报” **“可疑的 Powershell 命令行”。**  > 

5. 从调查窗口中，选择要前往 Microsoft Defender ATP 门户的链接。

## <a name="next-steps"></a>后续步骤

- [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
- [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何配置 Azure 订阅和资源组的安全策略。
- [在 Azure 安全中心管理安全建议](security-center-recommendations.md)：了解安全建议如何帮助保护 Azure 资源。
- [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
