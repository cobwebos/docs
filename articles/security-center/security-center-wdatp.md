---
title: Microsoft Defender 高级威胁防护 - Azure 安全中心
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
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: e1595d7e205c788f90177836f3c0370681b7747b
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569078"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender 高级威胁防护与 Azure 安全中心

Azure 安全中心与 [Microsoft Defender 高级威胁防护 (ATP) ](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) 集成，提供 (EDR) 功能的综合终结点检测和响应。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|**正式版**|
|计价|标准层|
|支持的计算机：|![是](./media/icons/yes-icon.png) 运行 Windows 的 Azure 计算机<br>![是](./media/icons/yes-icon.png) 运行 Windows 的 Azure Arc 计算机|
|必需的角色和权限：|启用/禁用集成： **安全管理员** 或 **所有者**<br>在安全中心内查看 MDATP 警报： **安全读者**、 **读者**、 **资源组参与者**、 **资源组所有者**、 **安全管理员**、 **订阅所有者**或 **订阅参与者**|
|云：|![是](./media/icons/yes-icon.png) 商业云。<br>![否](./media/icons/no-icon.png) 在公共 Azure 云中运行工作负荷的 GCC 客户<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中国 Gov，其他 Gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>安全中心的 Microsoft Defender ATP 功能

Microsoft Defender ATP 提供：

- **高级入侵后检测传感器**：Windows 服务器的 Microsoft Defender ATP 传感器收集各种各样的行为信号。

- **基于分析的、由云提供支持的入侵后检测**：Microsoft Defender ATP 可快速应对不断变化的威胁。 它使用高级分析和大数据。 Microsoft Defender ATP 借助 Intelligent Security Graph 的强大功能得以增强，并结合 Windows、Azure 和 Office 中的信号来检测未知威胁。 它提供可以采取措施的警报，并可让你快速做出响应。

- **威胁智能**：Microsoft Defender ATP 在识别攻击者工具、方法和过程时生成警报。 它使用 Microsoft 威胁猎人和安全团队生成的，并由合作伙伴提供的情报补充的数据。


通过将 Defender ATP 与 Azure 安全中心集成，你还可以从以下附加功能中获益：

- **自动载入**：集成会自动为 Azure 安全中心监视的 windows 服务器启用 MICROSOFT Defender ATP 传感器 (除非它们运行的是 windows Server 2019) 。

- **单一虚拟管理平台**：Azure 安全中心控制台显示 Microsoft Defender ATP 警报。 若要进一步调查，请使用 Microsoft Defender ATP。 Microsoft Defender ATP 提供其他信息，例如警报进程树和事件图形。 此外，还可以看到详细的机器时间线，其中显示了最长六个月的历史时段的每种行为。

    ![Microsoft Defender ATP 页，其中包含有关警报的详细信息](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支持

安全中心的 Microsoft Defender ATP 支持在 Windows Server 2016、2012 R2 和 2008 R2 SP1 上检测。 对于 Azure Vm，你需要标准层订阅，对于非 Azure Vm，你只需在工作区级别使用 "标准" 层。

已为 Office 365 GCC 客户禁用使用此集成的服务器终结点监视。

## <a name="data-storage-location"></a>数据存储位置

使用 Azure 安全中心监视服务器时，将自动创建 Microsoft Defender ATP 租户。 Microsoft Defender ATP 收集的数据存储在设置期间标识的租户地理位置。 Pseudonymized 窗体中的客户数据还可以存储在美国中的中央存储和处理系统中。 

配置后便无法更改存储数据的位置。 如果需要将数据移动到另一个位置，请联系 Microsoft 支持部门重置租户。


## <a name="onboard-servers-to-security-center"></a>将服务器载入到安全中心 

若要将服务器加入到安全中心，请从“Microsoft Defender ATP 服务器加入”单击“转到 Azure 安全中心加入服务器”。

1. 在“加入”区域中选择或创建用于存储数据的工作区。

2. 如果看不到所有工作区，原因可能是缺少权限，请确保工作区设置为 "Azure 安全标准" 定价层。 有关详细信息，请参阅 [升级到安全中心的标准层以增强安全性](security-center-pricing.md)。
    
3. 选择“添加服务器”以查看有关如何安装 Log Analytics 代理的说明。 

4. 加入后，可以在“计算和应用”下监视计算机。

   ![加入计算机](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>启用 Microsoft Defender ATP 集成

若要查看是否已启用 Microsoft Defender ATP 集成，请选择“安全中心” > “定价与设置”> 单击你的订阅 。
在此处可以查看当前已启用的集成。

  ![Azure 安全中心威胁检测设置页，其中显示已启用 Microsoft Defender ATP 集成](media/security-center-wdatp/enable-integrations.png)

- 如果已将服务器载入 Azure 安全中心标准层，则不需要执行进一步的操作。 Azure 安全中心会自动将服务器加入 Microsoft Defender ATP。 加入过程可能最多需要 24 小时。

- 如果你从未将服务器载入 Azure 安全中心标准层，请照常将它们载入 Azure 安全中心。

- 如果通过 Windows Defender ATP 加入了服务器：
  - 请参阅有关[如何卸载服务器计算机](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南文档。
  - 将这些服务器载入 Azure 安全中心。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>访问 Microsoft Defender ATP 门户

1. 遵照[为用户分配门户访问权限](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)中的说明操作。

1. 检查你的代理或防火墙是否正在阻止匿名通信。 Defender ATP 传感器连接到系统上下文，因此必须允许匿名通信。 若要确保受阻碍访问 Microsoft Defender ATP 门户，请按照在 [代理服务器中启用对 Microsoft DEFENDER atp 服务 url 的访问](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的说明进行操作。

## <a name="test-the-feature"></a>测试功能

生成良性的 Microsoft Defender ATP 测试警报：

1. 创建文件夹“C:\test-MDATP-test”。

1. 使用远程桌面访问 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。 打开命令行窗口。

1. 在提示符下，复制并运行以下命令。 命令提示符窗口将自动关闭。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![包含上述命令的命令提示符窗口](media/security-center-wdatp/image4.jpeg)

1. 如果该命令成功，则 Azure 安全中心仪表板和 Microsoft Defender ATP 门户中会显示一条新警报。 此警报可能要在几分钟之后才显示。

1. 若要在安全中心查看该警报，请转到“安全警报” > “可疑的 PowerShell 命令行” 。

1. 在调查窗口中，选择相应的链接转到 Microsoft Defender ATP 门户。

## <a name="next-steps"></a>后续步骤

- [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
- [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何为 Azure 订阅和资源组配置安全策略。
- [管理 Azure 安全中心的安全建议](security-center-recommendations.md)：了解建议如何帮助你保护 Azure 资源。
- [在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。