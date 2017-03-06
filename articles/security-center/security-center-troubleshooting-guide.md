---
title: "Azure 安全中心故障排除指南 | Microsoft Docs"
description: "本文档可以帮助排除 Azure 安全中心中的问题。"
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: b9f4a8b185f9fb06f8991b6da35a5d8c94689367
ms.openlocfilehash: dbbec729c14d0d9dc5781e7a88a1db3f66f7df97
ms.lasthandoff: 02/16/2017


---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure 安全中心故障排除指南
本指南适用于信息技术 (IT) 专业人员、信息安全分析人员，以及那些组织中正在使用 Azure 安全中心并需要进行排除安全中心相关问题的云管理员。

## <a name="troubleshooting-guide"></a>故障排除指南
本指南介绍如何排除安全中心的相关问题。 安全中心完成的大部分故障排除将首先通过查看失败组件的 [审核日志](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) 记录展开。 通过审核日志，你可以确定：

* 哪些操作已发生
* 谁启动了该操作
* 操作何时发生
* 操作的状态
* 其他可能有助于研究操作的属性值

审核日志包含针对资源执行的所有写入操作（PUT、POST、DELETE），但它不包含读取属性 (GET)。

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Windows 中的监视代理安装故障排除
安全中心监视代理用于执行数据收集。 启用数据收集并在目标计算机中正确安装代理后，应执行以下进程：

* ASMAgentLauncher.exe - Azure 监视代理 
* ASMMonitoringAgent.exe - Azure 安全监视扩展
* ASMSoftwareScanner.exe – Azure 扫描管理器

Azure 安全监视扩展将扫描各种安全相关的配置，并从虚拟机中收集安全日志。 扫描管理器将用作修补扫描仪。

如果成功执行安装，应该会看到一个与下面目标 VM 的审核日志中类似的条目：

![审核日志](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

此外可以通过读取位于 *%systemdrive%\windowsazure\logs*（示例：C:\WindowsAzure\Logs）的代理日志，获取有关安装过程的详细信息。

> [!NOTE]
> 如果 Azure 安全中心代理产生错误行为，将需要重新启动目标 VM，因为没有命令来停止和启动代理。


如果仍有数据收集方面的问题，可按以下步骤卸载代理：

1. 在 **Azure 门户**中选择存在数据收集问题的虚拟机，然后单击“扩展”。
2. 右键单击“Microsoft.Azure.Security.Monitoring”，然后选择单击“卸载”。

![删除代理](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig4.png)

Azure 安全监视扩展应该会在数分钟内自行重新安装。

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Linux 中的监视代理安装故障排除
在 Linux 系统中进行 VM 代理安装故障排除时，应确保该扩展已下载到 /var/lib/waagent/。 可以运行以下命令验证其是否已安装：

`cat /var/log/waagent.log` 

可以查看的用于故障排除的其他日志文件有： 

* /var/log/mdsd.err
* /var/log/azure/

在正常运行的系统中，应该会看到 TCP 29130 上 mdsd 进程的连接。 这是与 mdsd 进程进行通信的系统日志。 可以通过运行以下命令来验证此行为：

`netstat -plantu | grep 29130`

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>有关终结点保护无法正常工作的疑难解答

来宾代理是 [Microsoft 反恶意软件](../security/azure-security-antimalware.md)扩展进行的所有操作的父进程。 当来宾代理进程故障时，作为来宾代理子进程运行的 Microsoft 反恶意软件也可能发生故障。  这类情况下，建议验证以下选项：

- 目标 VM 是否是自定义映像，VM 的创建者是否从未安装过来宾代理。
- 如果目标是 Linux VM 而非 Windows VM，则在 Linux VM 上安装 Windows 版反恶意软件扩展将失败。 Linux 来宾代理对操作系统版本和所需程序包有特定要求，如不满足这些要求，则 VM 代理也无法正常工作。 
- 是否使用旧版来宾代理创建了 VM。 如果是，请注意某些旧版代理不会自动更新到较新版本，这可能导致出现此问题。 创建自己的映像时，请始终使用最新版本的来宾代理。
- 某些第三方管理软件可能会禁用来宾代理，或阻止对某些文件位置的访问。 如果 VM 上安装有第三方管理软件，请确保代理在排除列表中。
- 某些防火墙设置或网络安全组 (NSG) 可能会阻止发送至来宾代理的流量和来宾代理发送出的流量。
- 某些访问控制列表 (ACL) 可能会阻止磁盘访问。
- 磁盘空间不足可能会阻止来宾代理正常工作。 

默认情况下禁用 Microsoft 反恶意软件用户界面，请参阅 [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/)（部署后在 Azure Resource Manager VM 上启用 Microsoft 反恶意软件用户界面），深入了解如何在需要时启用该用户界面。

## <a name="troubleshooting-problems-loading-the-dashboard"></a>排查加载仪表板的问题

如果在加载“安全中心”仪表板时遇到问题，请确保将订阅注册到安全中心的用户（即第一个通过订阅打开安全中心的用户）以及需要启用数据收集功能的用户为订阅的*所有者* 或*参与者*。 从该时刻开始，在订阅上为*读者* 的 also user 即可查看 dashboard/alerts/recommendation/policy。

## <a name="contacting-microsoft-support"></a>请联系 Microsoft 支持人员
可以使用本文中提供的指南来识别一些问题，还可以在安全中心公共 [论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)中查找记录的其他问题。 但是，如果需要进一步进行故障排除，可以使用 **Azure 门户**打开新的支持请求，如下所示： 

![Microsoft 支持部门](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>另请参阅
在本文档中，已经学习了如何在 Azure 安全中心中配置安全策略。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md) - 了解如何规划和了解设计注意事项，以适应 Azure 安全中心。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章


