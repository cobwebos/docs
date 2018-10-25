---
title: Windows Defender 高级威胁防护与 Azure 安全中心
description: 本文档介绍 Azure 安全中心与 Windows Defender 高级威胁防护之间的集成。
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: e7bb7f0c4cb63390b91012e071589611991a2866
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068322"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender 高级威胁防护与 Azure 安全中心

Azure 安全中心通过与 [Windows Defender 高级威胁防护](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP) 相集成来扩展其云工作负荷保护平台产品/服务。
此项更改带来了全面的终结点检测和响应 (EDR) 功能。 使用 Windows Defender ATP 集成可以查明异常。 还可以检测和响应 Azure 安全中心所监视的服务器终结点上出现的高级攻击。

Azure 安全中心客户现在可以使用 Windows Defender ATP 的功能：

- **下一代后期安全漏洞检测传感器**：Windows 服务器的 Windows Defender ATP 传感器收集各种各样的行为信号。

- **基于分析的由云支持的后期安全漏洞检测**：Windows Defender ATP 能够快速适应不断变化的威胁。 它使用高级分析和大数据。 Windows Defender ATP 借助 Intelligent Security Graph 的强大功能得以增强，并结合 Windows、Azure 和 Office 中的信号来检测未知威胁。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁智能**：Windows Defender ATP 可识别攻击者使用的工具、方法和过程。 检测这些信息后，它会生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。

以下功能现已在 Azure 安全中心推出：

- **自动载入**：对于载入 Azure 安全中心的 Windows 服务器，将自动启用 Windows Defender ATP 传感器。

- **单一监视视图**：Azure 安全中心控制台显示 Windows Defender ATP 警报。

- **详细的机器调查**：Azure 安全中心客户可以访问 Windows Defender ATP 控制台来执行详细调查，以发现安全漏洞的范围。

![Azure 安全中心，其中显示了警报列表以及有关每条警报的一般信息](media/security-center-wdatp/image1.png)

可以在 Azure 安全中心[调查警报](security-center-investigation.md)：

![Azure 安全中心的警报调查仪表板](media/security-center-wdatp/image2.png)

可以通过透视 Windows Defender ATP 进一步调查警报。 在其中可以看到其他信息，例如警报进程树和事件图形。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

![Windows Defender ATP 页，其中包含有关警报的详细信息](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支持

此功能支持在 Windows Server 2012 R2 和 Windows Server 2016 上进行检测。

仅支持标准服务层的订阅中的服务器。

## <a name="onboarding-instructions"></a>载入说明

若要查看是否启用了 Windows Defender ATP 集成，请选择“安全中心” > “安全策略” > “订阅” > “编辑设置”。

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

如果当 Windows Defender ATP 传感器从系统上下文建立连接时代理或防火墙阻止了匿名流量，请确保允许匿名流量。 遵照[在代理服务器中启用对 Windows Defender ATP 服务 URL 的访问](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server)中的说明操作。

## <a name="test-the-feature"></a>测试功能

生成良性的 Windows Defender ATP 测试警报：

1. 使用远程桌面访问 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。  打开命令提示符窗口。

2. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![包含上述命令的命令提示符窗口](media/security-center-wdatp/image4.jpeg)

3. 如果该命令成功，则 Azure 安全中心仪表板和 Windows Defender ATP 门户中会显示一条新警报。 此警报可能要在几分钟之后才显示。

4. 若要在安全中心查看该警报，请转到“安全警报” >  “可疑的 Powershell 命令行”。

5. 在调查窗口中，选择相应的链接转到 Windows Defender ATP 门户。

## <a name="next-steps"></a>后续步骤

- [在 Azure 安全中心设置安全策略](security-center-policies.md)：了解如何配置 Azure 订阅和资源组的安全策略。
- [在 Azure 安全中心管理安全建议](security-center-recommendations.md)：了解安全建议如何帮助保护 Azure 资源。
- [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
