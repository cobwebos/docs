---
title: "Azure 日志集成常见问题解答 |Microsoft 文档"
description: "本文回答了关于 Azure 日志的问题。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 08/07/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: bc543cdf3d375076af5b5f17f8ca216dd5bf72b0
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---
# <a name="azure-log-integration-faq"></a>Azure 日志集成常见问题解答
本文回答了一些有关 Azure 日志集成常见问题 (FAQ)。 

Azure 日志集成是 Windows 操作系统服务，可用来将 Azure 资源中的原始日志集成到本地安全信息和事件管理 (SIEM) 系统。 此集成为本地或云端的所有资产提供统一的仪表板。 对于与应用程序相关的安全事件，可进行聚合、关联、分析和警报等操作。

## <a name="is-the-azure-log-integration-software-free"></a>Azure 日志集成软件是否免费？
是的。 Azure 日志集成软件不产生任何费用。

## <a name="where-is-azure-log-integration-available"></a>Azure 日志集成在哪些地方可用？

目前 Azure 商业版和 Azure 政府版提供该功能，但在中国或德国不提供。

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>如何查看 Azure 日志集成从中提取 Azure VM 日志的存储帐户？
运行 **azlog source list** 命令。

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>如何判断 Azure 日志集成日志来自哪个订阅？

如果审核日志位于“AzureResourcemanagerJson”目录中，则日志文件名称中具有订阅 ID。 “AzureSecurityCenterJson”文件夹中的日志也是如此。 例如：

20170407T070805_2768037.0000000023.1111e5ee-1111-111b-a11e-1e111e1111dc.json

Azure Active Directory 审核日志的名称包含租户 ID。

从事件中心读取的诊断日志的名称不包括订阅 ID。 但包括在创建事件中心源的过程中指定的友好名称。 

## <a name="how-can-i-update-the-proxy-configuration"></a>如何更新代理配置？
如果代理设置不允许直接访问 Azure 存储，请打开 **c:\Program Files\Microsoft Azure Log Integration** 中的 **AZLOG.EXE.CONFIG** 文件。 更新文件，以将组织的代理地址包括在 **defaultProxy** 部分中。 完成更新后，并使用“net stop azlog”和“net start azlog”命令停止和启动该服务。

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>如何查看 Windows 事件中的订阅信息？
添加源时，将订阅 ID追加到友好名称之后：

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
事件 XML 具有以下元数据，包括订阅 ID：

![事件 XML][1]

## <a name="error-messages"></a>错误消息
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>运行 azlog createazureid 命令时，为什么会收到以下错误？
错误：

  *无法创建 AAD 应用程序 - 租户 72f988bf-86f1-41af-91ab-2d7cd011db37 - 原因 = “禁止” - 消息 = “特权不足以完成此操作”。*

azlog createazureid 命令尝试在 Azure 登录有权访问的订阅在所有 Azure AD 租户中创建服务主体。 如果 Azure 登录在 Azure AD 租户中只是来宾用户，该命令会失败，并显示“特权不足以完成此操作”。 请求租户管理员将帐户添加为租户中的用户。

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>运行 azlog authorize 命令时，为什么收到以下错误？
错误：

  *创建角色分配警告 - AuthorizationFailed：具有对象 ID“fe9e03e4-4dad-4328-910f-fd24a9660bd2”的客户端 janedo@microsoft.com 没有在“/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000”范围执行操作“Microsoft.Authorization/roleAssignments/write”的权限。*

azlog authorize 命令会将 Azure AD 服务主体的读取器角色（使用 azlog createazureid 创建）分配给提供的订阅。 如果 Azure 登录不是订阅的协同管理员或所有者，就会失败，并显示“授权失败”错误消息。 需要 Azure 基于角色访问控制 (RBAC) 的共同管理员或所有者来完成此操作。

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>在哪里可以找到审核日志中的属性定义？
请参阅：

* [使用 Azure 资源管理器执行审核操作](../azure-resource-manager/resource-group-audit.md)
* [在 Azure Monitor REST API 中列出订阅的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>哪里可以找到 Azure 安全中心警报的详细信息？
请参阅[管理和响应 Azure 安全中心中的安全警报](../security-center/security-center-managing-and-responding-alerts.md)。

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>如何修改使用 VM 诊断收集的内容？
有关如何获取、修改和设置 Azure 诊断配置的详细信息，请参阅[使用 PowerShell 在运行 Windows 的虚拟机上启用 Azure 诊断](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

以下示例将获取 Azure 诊断配置：

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

以下示例将修改 Azure 诊断配置。 在此配置中，仅从事件日志中收集事件 ID 4624 和事件 ID 4625。 从系统事件日志中收集用于 Azure 事件的 Microsoft 反恶意软件。 有关使用 XPath 表达式的详细信息，请参阅 [Consuming Events](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85))（使用事件）。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

以下示例将设置 Azure 诊断配置：

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

完成更改后，检查存储帐户，以确保收集正确的事件。

如果在安装和配置过程中遇到任何问题，请打开[支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 请选择“日志集成”作为需要请求支持的服务。

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>可以使用 Azure 日志集成将网络观察程序日志集成到 SIEM 吗？

Azure 网络观察程序生成大量的日志记录信息。 这些日志不发送到 SIEM。 网络观察程序日志唯一支持的目标是存储帐户。 Azure 日志集成不支持读取这些日志，也不向 SIEM 提供这些日志。

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

