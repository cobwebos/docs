---
title: "Azure BizTalk 服务发行说明 | Microsoft Docs"
description: "列出 Azure BizTalk 服务的已知问题"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 84ad965ed6ef1711fda983220cf004fdd48d290d
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="release-notes-for-azure-biztalk-services"></a>Azure BizTalk 服务发行说明
Microsoft Azure BizTalk 服务发行说明包含此版本中的已知问题。

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>BizTalk 服务 11 月更新版中的新增功能
* 可在 BizTalk 服务门户中启用静态加密。 请参阅[在 BizTalk 服务门户中启用静态加密](https://msdn.microsoft.com/library/azure/dn874052.aspx)。

## <a name="update-history"></a>更新历史记录
### <a name="october-update"></a>10 月更新
* 支持组织帐户：  
  * **应用场景**：使用 Microsoft 帐户（如 user@live.com).）注册了 BizTalk 服务部署。在此场景中，只能使用 Microsoft 用户帐户通过 BizTalk 服务门户管理 BizTalk 服务。 不可使用组织帐户。  
  * **应用场景**：在 Azure Active Directory 中使用组织帐户（例如 user@fabrikam.com 或 user@contoso.com).）注册了 BizTalk 服务部署。在此场景中，只能使用同一组织内部的 Azure Active Directory 用户帐户通过 BizTalk 服务门户管理 BizTalk 服务。 不可使用 Microsoft 帐户。  
* 在 Azure 经典门户中创建 BizTalk 服务时，会在 BizTalk 服务门户中自动注册。
  * **方案**：首次登录 Azure 经典门户、创建 BizTalk 服务，然后选择“管理”。 BizTalk 服务门户打开时，BizTalk 服务会自动注册并可供部署。  
    请参阅[在 BizTalk 服务门户中注册和更新 BizTalk 服务部署](https://msdn.microsoft.com/library/azure/hh689837.aspx)。  

### <a name="august-14-update"></a>8 月 14 日更新
* 协议和网桥分离 - 现已在 BizTalk 服务门户中分离贸易合作伙伴协议和网桥。 可单独创建协议和网桥，网桥在运行时基于 EDI 消息中的值解析为协议。 请参阅[在 Azure BizTalk 服务中创建协议](https://msdn.microsoft.com/library/azure/hh689908.aspx)、[使用 BizTalk 服务门户创建 EDI 桥](https://msdn.microsoft.com/library/azure/dn793986.aspx)、[使用 BizTalk 服务门户创建 AS2 桥](https://msdn.microsoft.com/library/azure/dn793993.aspx)和[桥在运行时如何解析协议？](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* 不可再为协议创建模板。  
* 对于发送端协议，现可为每个架构指定不同的分隔符集。 可在发送端协议的协议设置下指定此配置。 有关详细信息，请参阅[在 Azure BizTalk 服务中创建 X12 协议](https://msdn.microsoft.com/library/azure/hh689847.aspx)和[在 Azure BizTalk 服务中创建 EDIFACT 协议](https://msdn.microsoft.com/library/azure/dn606267.aspx)。 出于相同目的，还向 TPM OM API 添加了两个新实体。 请参阅 [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) 和 [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx)。  
* 现支持标准 XSD 构造，包括派生类型。 请参阅[在映射中使用标准 XSD 构造](https://msdn.microsoft.com/library/azure/dn793987.aspx)和[在映射方案和示例中使用派生类型](https://msdn.microsoft.com/library/azure/dn793997.aspx)。  
* AS2 支持用于消息签名的新 MIC 算法和新加密算法。 请参阅[在 Azure BizTalk 服务中创建 AS2 协议](https://msdn.microsoft.com/library/azure/hh689890.aspx)。  
  ## <a name="know-issues"></a>已知问题

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>更新 BizTalk 服务门户后出现的连接问题
  如果在 BizTalk 服务门户处于打开状态时升级了 BizTalk 服务以融入相关服务更改，可能会遇到 BizTalk 服务门户连接问题。  
  解决方法是重启浏览器，删除浏览器缓存，或者以专用模式启动门户。  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>如果在 BizTalk 服务项目中单击某个错误或警告，则 Visual Studio IDE 无法找到项目
安装 Visual Studio 2012 Update 3 RC 1 即可解决此问题。  

### <a name="custom-binding-project-reference"></a>自定义绑定项目引用
在 Visual Studio 解决方案中处理 BizTalk 服务项目时，请考虑以下情况：  

* 在同一 Visual Studio 解决方案中，有一个 BizTalk 服务项目和一个自定义绑定项目。 BizTalk 服务项目具有一个到此自定义绑定项目文件的引用。
* BizTalk 服务项目具有一个到自定义绑定/行为 DLL 的引用。

在 Visual Studio 中成功“生成”了解决方案。 然后，“重新生成”或“清理”该解决方案。 此后，再次重新生成或清除时，会出现以下错误：  
  无法将文件 <Path to DLL> 复制到“bin\Debug\FileName.dll”。 进程无法访问文件“bin\Debug\FileName.dll”，因为其他进程正使用此文件。  

#### <a name="workaround"></a>解决方法
* 如已安装 [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305)，则可使用以下两个选项：
  
  * 重启 Visual Studio，或者
  * 重启解决方案。 然后，只对解决方案执行“生成”操作。  
* 如未安装 [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305)，请打开任务管理器，然后依次单击“进程”选项卡、“MSBuild.exe”进程和“结束进程”按钮。  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>如果将不可打印字符升级为 HTTP 标头，则不可从网桥和 BizTalk 服务门户路由到 BasicHttpRelay 终结点
如果使用不可打印字符作为已升级消息属性的一部分，则无法将这些消息路由到使用 BasicHttpRelay 绑定的中继目标。 另外，可用作跟踪内容的已升级属性会对 blob 进行 URL 编码，并对目标取消编码。  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>即使取消选中“发送异步 MDN”选项，仍会按异步方式发送 MDN
请考虑此方案 - 如果选择“发送异步 MDN”复选框并指定要将异步 MDN 发送到的 URL，然后再取消选中“发送异步 MDN”复选框，则即使此时未选择“发送异步 MDN”选项，仍会将 MDN 发送到指定 URL。  
解决方法是，取消选中“发送异步 MDN”复选框之前，必须清除指定 URL，然后部署 AS2 协议。  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>超出有效交换的空格字符会导致向挂起终结点发送空消息
如果有空格超出了 IEA 段，拆装器会将此情况视为当前交换结束，并查找代表下一条消息的下一组空格。 由于这不是有效交换，因此可能看到有一条成功消息已发送到路由目标，同时一条空消息已发送到挂起终结点。  

### <a name="tracking-in-biztalk-services-portal"></a>在 BizTalk 服务门户中跟踪
在 EDI 消息处理和任何关联中捕获跟踪事件。 如果消息处理在“协议”阶段之外失败，“跟踪”将显示为处理成功。 此情况下，请参阅“跟踪”中“详细信息”列下面的“日志”部分，了解错误详细信息。
有关协议阶段的相关信息，请参阅 X12 接收和发送设置（[在 Azure BizTalk 服务中创建 X12 协议](https://msdn.microsoft.com/library/azure/hh689847.aspx)）。  

### <a name="update-agreement"></a>更新协议
配置协议时，可使用 BizTalk 服务门户修改标识限定符。 这可能导致属性不一致。 例如，有协议使用 ZZ:1234567 和 ZZ:7654321 作为限定符。 在 BizTalk 服务门户配置文件设置中，将 ZZ:1234567 更改为 01:ChangedValue。 打开协议时，显示 01:ChangedValue 而不是 ZZ:1234567。
若要修改标识限定符，请删除协议，更新合作伙伴配置文件中的**标识**，然后重新创建该协议。  

> AZURE.WARNING 此行为会影响 X12 和 AS2。  
> 
> 

### <a name="as2-attachments"></a>AS2 附件
发送或接收设置中不支持 AS2 消息的附件。 具体而言，系统以无提示方式忽略附件，并将消息正文作为普通 AS2 消息处理。  

### <a name="resources-remembering-path"></a>资源：记住路径
添加“资源”时，对话框窗口可能不记得之前用于添加资源的路径。 若要记住以前使用的路径，请尝试将 BizTalk 服务门户网站添加到 Internet Explorer 的“受信任的站点”中。  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>如果重命名网桥的实体名称，并关闭项目而不保存更改，则再次打开该实体会导致出错
请考虑按以下顺序执行操作的方案：  

* 将网桥（例如 XML 单向桥）添加到 BizTalk 服务项目  
* 通过指定“实体名称”属性的值重命名该网桥。 这会使用指定的名称重命名关联的 .bridgeconfig 文件。  
* 关闭 .bcs 文件（在 Visual Studio 中关闭相应选项卡）且不保存更改。  
* 从解决方案资源管理器中再次打开该 .bcs 文件。  
  你将注意到，尽管关联的 .bridgeconfig 文件使用指定的新名称，但设计图面上的实体名仍是旧名称。 如果尝试通过双击网桥组件打开网桥配置，会出现以下错误：  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` 为避免出现此情况，请确保在 BizTalk 服务项目中重命名实体后保存更改。  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>即使从 Visual Studio 项目中排除某项目，BizTalk 服务项目仍可成功生成
请考虑此方案：先将项目（如 XSD 文件）添加到 BizTalk 服务项目，再将该项目包含在网桥配置中（例如通过将其指定为“请求”消息类型），然后从 Visual Studio 项目中排除它。 在此情况下，只要已删除项目在将其包含到 Visual Studio 项目时所在的同一磁盘位置中存在，生成项目时就不会发出任何错误。
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk 服务项目在配置网桥时不检查架构可用性
在 BizTalk 服务项目中，如果添加到项目中的某个架构导入了其他架构，则 BizTalk 服务项目不检查导入的架构是否已添加到项目。 如果尝试生成此类项目，则不会收到任何生成错误。
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>XML“请求-答复”网桥的响应消息始终采用字符集 UTF-8
对于此版本，来自 XML“请求-答复”网桥的响应消息字符集始终设置为 UTF-8。
  
### <a name="user-defined-datatypes"></a>用户定义的数据类型
BizTalk 适配器服务功能中的 BizTalk 适配器包适配器可利用用户定义的数据类型完成适配器操作。
使用用户定义的数据类型时，请将文件 (.dll) 复制到 drive:\Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\，或者复制到托管 BizTalk 适配器服务的服务器上的全局程序集缓存 (GAC)。 否则，客户端上可能出现以下错误：  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> 建议使用 GACUtil.exe 将文件安装到全局程序集缓存中。 GACUtil.exe 记录了如何使用此工具和 Visual Studio 命令行选项。  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>重启 BizTalk 适配器服务网站
通过安装 **BizTalk 适配器服务运行时** *会在 IIS 中创建包含* *BAService* *应用程序的* *BizTalk 适配器服务 网站** 。**BAService** 应用程序在内部使用中继绑定将本地服务终结点的工作范围扩展到云中。 对于本地托管的服务，仅在该本地服务启动时，才在服务总线上注册相应的中继终结点。  

如果停止后再启动应用程序，系统不会接受用于自动启动应用程序的配置。 因此，停止 **BAService** 后，必须始终改为重启 **BizTalk 适配器服务**网站。 请勿启动或停止 **BAService** 应用程序。

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>不应在 LOB 组件的地址和实体名称中使用特殊字符
不应在 LOB 组件的地址和实体名称中使用特殊字符。 否则，部署 BizTalk 服务项目时将收到错误。 使用某些字符（例如“%”）时，BizTalk 适配器服务网站可能进入停止状态，此时必须手动启动此网站。

### <a name="test-map-with-get-context-property"></a>使用“获取上下文属性”测试映射
如果转换包含“获取上下文属性”映射操作，**测试映射**将失败。 临时解决方法是，将“获取上下文属性”映射操作替换为包含虚拟数据的字符串连接映射操作。 这会填充目标架构，并允许测试其他转换功能。

### <a name="test-map-property-does-not-display"></a>不显示测试映射属性
Visual Studio 中不显示**测试映射**属性。 如果“属性”窗口和“解决方案资源管理器”窗口未同时停靠，可能会出现此问题。 若要解决此问题，请停靠“属性”窗口和“解决方案资源管理器”窗口。  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>“重新设置日期时间格式”下拉列表呈灰显
将“重新设置日期时间格式”映射操作添加到设计图面并对其进行配置后，“格式”下拉列表可能灰显。 如果计算机“显示”设置为“中等 - 125%”或“较大 - 150%”，则可能发生此情况。 若要解决此问题，请按照以下步骤将显示大小设置为“较小 - 100% (默认)”：  

1. 打开“控制面板”，然后单击“外观和个性化”。
2. 单击“显示”。
3. 单击“较小 - 100% (默认)”，然后单击“应用”。

“格式”下拉列表现应按预期方式工作。

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>BizTalk 服务门户中出现重复协议
请考虑以下方案：

1. 使用贸易合作伙伴管理 OM API 创建一个协议。
2. 在 BizTalk 服务门户的两个不同选项卡中打开该协议。
3. 从这两个选项卡中部署该协议。
4. 由此，部署了两个协议，导致 BizTalk 服务门户中出现重复项

**解决方法**： 在 BizTalk 服务门户中打开并取消部署任一重复协议。  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>即使某证书已在项目存储中更新，网桥也不使用更新后的证书
请考虑以下方案：  

**方案 1：使用基于指纹的证书保护从网桥到服务终结点的消息传输**  
请考虑在 BizTalk 服务项目中使用基于指纹的证书的方案。 在 BizTalk 服务门户中使用相同名称但不同指纹更新了证书，但未更新相应的 BizTalk 服务项目。 在此情况下，网桥可能继续处理消息，因为旧证书数据可能仍在通道缓存中。 随后，消息处理失败。  

**解决方法**：更新 BizTalk 服务项目中的证书，然后重新部署该项目。  

**方案 2：使用基于名称的行为识别用于保护从网桥到服务终结点的消息传输的证书**

请考虑在 BizTalk 服务项目中使用基于名称的行为来识别证书的方案。 在 BizTalk 服务门户中更新了证书，但未更新相应 BizTalk 服务项目。 在此情况下，网桥可能继续处理消息，因为旧证书数据可能仍在通道缓存中。 随后，消息处理失败。  

**解决方法**：更新 BizTalk 服务项目中的证书，然后重新部署该项目。  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>即使 SQL 数据库处于离线状态，网桥也会继续处理消息
即使 Microsoft Azure SQL 数据库（用于存储运行中信息，例如已部署的项目和管道）处于离线状态，BizTalk 服务桥在一段时间内仍会继续处理消息。 这是因为 BizTalk 服务使用缓存的项目和网桥配置。
如果希望网桥在 SQL 数据库离线时不处理任何消息，可使用 BizTalk 服务 PowerShell cmdlet 停止或挂起 BizTalk 服务。 有关用于管理操作的 Windows PowerShell cmdlet，请参阅 [Azure BizTalk 服务管理示例](http://go.microsoft.com/fwlink/p/?LinkID=329019)。  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>阅读网桥自定义代码组件中的 XML 消息，其中包含多余的 BOM 字符
请考虑此场景：你想要阅读网桥自定义代码中的 XML 消息。 如果使用 .NET API System.Text.Encoding.UTF8.GetString(bytes)，则多余的 BOM 字符包含在消息开头的输出中。 因此，如果不希望输出包含多余的 BOM 字符，必须使用 ```System.IO.StreamReader().ReadToEnd()```。

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>向使用 WCF 的网桥发送消息时无法缩放
发送到使用 WCF 的网桥的消息无法缩放。 如果希望客户端可缩放，应改用 HttpWebRequest。

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>升级：从 BizTalk 服务预览版升级到正式版 (GA) 后发生令牌提供程序错误
存在使用活动批处理的 EDI 或 AS2 协议。 将 BizTalk 服务从预览版升级到 GA 后，可能发生以下错误：

* 错误：令牌提供程序无法提供安全令牌。 令牌提供程序返回了消息：无法解析远程名称。
* 已取消批任务。

**解决方法**：将 BizTalk 服务更新到正式版 (GA) 后，重新部署协议。  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>升级：升级 BizTalk 服务 SDK 后，工具箱显示旧的网桥图标
升级旧版 BizTalk 服务 SDK（使用旧图标表示网桥）后，工具箱仍显示网桥的旧图标。 但是，如果将网桥添加到 BizTalk 服务项目设计图面，图面中显示新图标。  

**解决方法**： 可删除 <system drive>:\Users\<user>\AppData\Local\Microsoft\VisualStudio\11.0 下的 .tbd 文件解决此问题。  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>升级：将 BizTalk 门户从预览版更新到 GA 后，可能显示指出 EDI 功能不可用的错误消息
将 BizTalk 服务从预览版升级到 GA 后，如果登录到 BizTalk 服务门户，门户中可能出现以下错误：  

本功能在此版本的 Microsoft Azure BizTalk 服务中不可用。 若要使用这些功能，请切换到适当版本。  

**解决方法**：退出该门户，关闭再打开浏览器，然后登录到门户。  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>升级：将 BizTalk 服务升级到 GA 后不显示新的跟踪数据
假设存在这种情况：你在 BizTalk 服务预览版订阅中部署了一个 XML 桥。 向该桥发送消息时，BizTalk 服务门户中提供相应的跟踪数据。 现在，如果将 BizTalk 服务门户和 BizTalk 服务运行时位升级到 GA，再向以前部署的同一桥终结点发送消息，则不显示升级后发送的消息的跟踪数据。  

### <a name="pipelines-versus-bridges"></a>管道与网桥
在整篇文档中，术语“管道”和“网桥”可换用。 两者的含义基本相同，都表示 BizTalk 服务上部署的消息处理单元。  

### <a name="concepts"></a>概念
[BizTalk 服务](https://msdn.microsoft.com/library/azure/hh689864.aspx)   


