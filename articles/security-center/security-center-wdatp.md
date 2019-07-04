---
title: Windows Defender 高级威胁防护与 Azure 安全中心
description: 本文档介绍 Azure 安全中心与 Windows Defender 高级威胁防护之间的集成。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: monhaber
ms.openlocfilehash: 1737856067b2490db4a993b4383b320cb13a7774
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551774"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender 高级威胁防护与 Azure 安全中心

Azure 安全中心通过与 [Windows Defender 高级威胁防护](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP) 相集成来扩展其云工作负荷保护平台产品/服务。
此项更改带来了全面的终结点检测和响应 (EDR) 功能。 使用 Windows Defender ATP 集成可以查明异常。 还可以检测和响应 Azure 安全中心所监视的服务器终结点上出现的高级攻击。

## <a name="windows-defender-atp-features-in-security-center"></a>安全中心中的 Windows Defender ATP 功能

当使用 Windows Defender ATP 你收到：

- **下一代入侵后检测传感器**：Windows 服务器的 Windows Defender ATP 传感器收集各种各样的行为信号。

- **基于分析的、由云提供支持的入侵后检测**：Windows Defender ATP 可快速应对不断变化的威胁。 它使用高级分析和大数据。 Windows Defender ATP 借助 Intelligent Security Graph 的强大功能得以增强，并结合 Windows、Azure 和 Office 中的信号来检测未知威胁。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁智能**：Windows Defender ATP 可识别攻击者使用的工具、方法和过程。 检测这些信息后，它会生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

以下功能现已在 Azure 安全中心推出：

- **自动加入**：对于加入 Azure 安全中心的 Windows 服务器，将自动启用 Windows Defender ATP 传感器。

- **单一虚拟管理平台**：Azure 安全中心控制台显示 Windows Defender ATP 警报。

- **详细的计算机调查**：Azure 安全中心客户可以访问 Windows Defender ATP 控制台来执行详细调查，以发现入侵的范围。

![Azure 安全中心，其中显示了警报列表以及有关每条警报的一般信息](media/security-center-wdatp/image1.png)

可以通过透视 Windows Defender ATP 进一步调查警报。 在其中可以看到其他信息，例如警报进程树和事件图形。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

![Windows Defender ATP 页，其中包含有关警报的详细信息](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支持

安全中心中的 Windows Defender ATP 支持检测在 Windows Server 2012 R2 和 Windows Server 2016 属于标准服务订阅的操作系统上。

> [!NOTE]
> 当你使用 Azure 安全中心监视服务器时，Windows Defender ATP 租户自动创建和默认的 Windows Defender ATP 数据存储在欧洲。 如果你需要将数据移到另一个位置，你需要联系 Microsoft 支持部门重置租户。

## <a name="onboarding-servers-to-security-center"></a>将服务器加入安全中心 

若要将服务器加入到安全中心，请从“Windows Defender ATP 服务器加入”单击“到 Azure 安全中心加入服务器”  。

1. 在“加入”  边栏选项卡中选择或创建用于存储数据的工作区。 <br>
2. 如果看不到所有工作区，可能是由于缺少权限，请确保已将工作区设置到“Azure 安全标准”层。 有关详细信息，请参阅[升级到安全中心的标准层以增强安全性](security-center-pricing.md)。
    
3. 选择“添加服务器”  以查看有关如何安装 Microsoft Monitoring Agent 的说明。 

4. 加入后，可以在“计算和应用”  下监视计算机。

   ![加入计算机](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>启用 Windows Defender ATP 集成

若要查看如果启用了 Windows Defender ATP 集成，请选择**安全中心** > **定价和设置**> 单击你的订阅。

  ![Azure 安全中心策略管理](media/security-center-wdatp/policy-management.png)

在此处可以查看当前已启用的集成。

  ![Azure 安全中心威胁检测设置页，其中显示已启用 Windows Defender ATP 集成](media/security-center-wdatp/enable-integrations.png)

- 如果已将服务器载入 Azure 安全中心标准层，则不需要执行进一步的操作。 Azure 安全中心会自动将服务器载入 Windows Defender ATP。 此过程可能最多需要 24 小时。

- 如果你从未将服务器载入 Azure 安全中心标准层，请照常将它们载入 Azure 安全中心。

- 如果通过 Windows Defender ATP 载入了服务器：
  - 请参阅有关[如何卸载服务器计算机](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南文档。
  - 将这些服务器载入 Azure 安全中心。

## <a name="access-to-the-windows-defender-atp-portal"></a>访问 Windows Defender ATP 门户

遵照[为用户分配门户访问权限](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)中的说明操作。

## <a name="set-the-firewall-configuration"></a>设置防火墙配置

如果当 Windows Defender ATP 传感器从系统上下文建立连接时代理或防火墙阻止了匿名流量，请确保允许匿名流量。 遵照[在代理服务器中启用对 Windows Defender ATP 服务 URL 的访问](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的说明操作。

## <a name="test-the-feature"></a>测试功能

生成良性的 Windows Defender ATP 测试警报：

1. 使用远程桌面访问 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。  打开命令提示符窗口。

2. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![包含上述命令的命令提示符窗口](media/security-center-wdatp/image4.jpeg)

3. 如果该命令成功，则 Azure 安全中心仪表板和 Windows Defender ATP 门户中会显示一条新警报。 此警报可能要在几分钟之后才显示。

4. 若要在安全中心查看该警报，请转到“安全警报”>  “可疑的 Powershell 命令行”     。

5. 在调查窗口中，选择相应的链接转到 Windows Defender ATP 门户。

## <a name="next-steps"></a>后续步骤

- [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何为 Azure 订阅和资源组配置安全策略。
- [管理 Azure 安全中心的安全建议](security-center-recommendations.md)：了解建议如何帮助你保护 Azure 资源。
- [在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
