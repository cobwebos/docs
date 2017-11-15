---
title: "连接器版本发行历史记录 | Microsoft Docs"
description: "本主题列出了 Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的连接器的所有版本"
services: active-directory
documentationcenter: 
author: fimguy
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: fimguy
ms.openlocfilehash: e6df124a38c748294e92183df272dc266a0afc51
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="connector-version-release-history"></a>连接器版本发行历史记录
Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的连接器会经常更新。

> [!NOTE]
> 本主题仅适用于 FIM 和 MIM。 不支持将这些连接器安装在 Azure AD Connect 上。 升级到指定的版本时，已发布的连接器将预安装在 AADConnect 上。


本主题列出所有已发布的连接器版本。

相关链接：

* [下载最新连接器](http://go.microsoft.com/fwlink/?LinkId=717495)
* [泛型 LDAP 连接器](active-directory-aadconnectsync-connector-genericldap.md)参考文档
* [泛型 SQL 连接器](active-directory-aadconnectsync-connector-genericsql.md)参考文档
* [Web 服务连接器](http://go.microsoft.com/fwlink/?LinkID=226245)参考文档
* [PowerShell 连接器](active-directory-aadconnectsync-connector-powershell.md)参考文档
* [Lotus Domino 连接器](active-directory-aadconnectsync-connector-domino.md)参考文档

## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>已解决的问题：

* Lotus Notes：
  * 筛选自定义认证者选项
  * 修复了类 ImportOperations 的导入，以便决定哪些操作可在“示图”模式下运行，哪些操作可在“搜索”模式下运行。
* 泛型 LDAP：
  * OpenLDAP 目录使用 DN 而非 entryUUI 作为定位点。 增加了 GLDAP 连接器的新选项，用于修改定位点
* 泛型 SQL：
  * 修复了具有 varbinary(max) 类型的导出到字段。
  * 将二进制数据从数据源添加到 CSEntry 对象时，DataTypeConversion 函数在零字节时失败。 修复了 CSEntryOperationBase 类的 DataTypeConversion 函数。




### <a name="enhancements"></a>增强功能：

* 泛型 SQL：
  * 在“全局参数”页下的通用 SQL 管理代理配置窗口中，添加了具有命名参数或未命名参数的执行存储过程的模式配置功能。 在“全局参数”页中有一个复选框（其标签为“使用命名参数执行存储过程”），用于选择执行存储过程的模式是否使用命名参数。
    * 当前，仅限在数据库 IBM DB2 和 MSSQL 中执行具有命名参数的存储过程。 对于数据库 Oracle 和 MySQL，此方法不起作用： 
      * MySQL 的 SQL 语法不支持存储过程中的命名参数。
      * 用于 Oracle 的 ODBC 驱动程序不支持存储过程中的命名参数。

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>已解决的问题：

* 泛型 Web 服务：
  * 解决了存在两个或更多终结点时无法创建 SOAP 项目的问题。
* 泛型 SQL：
  * 在导入操作中，GSQL 在保存到连接器空间时未正确转换时间。 GSQL 连接器空间的默认日期和时间格式已从“yyyy-MM-dd hh:mm:ssZ”更改为“yyyy-MM-dd HH:mm:ssZ”。

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>已解决的问题：

* 泛型 Web 服务：
  * Wsconfig 工具未从 REST 服务方法的“示例请求”中正确转换 Json 数组。 这导致序列化 REST 请求的此 Json 数组时出现问题。
  * Web 服务连接器配置工具不支持在 JSON 属性名称中使用空间符号 
    * 可以将替换模式手动添加到 WSConfigTool.exe.config 文件，例如 ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes：
  * 如果禁用“允许组织/组织单位的自定义认证者”选项，则连接器将在导出（更新）过程中失败。导出流程完成后，所有属性都会导出到 Domino 中，但在导出时，将返回一个 KeyNotFoundException 到同步。 
    * 这是因为重命名操作在尝试通过更改以下属性之一来更改 DN（用户名属性）时失败：  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - ou
      - altcommonname

  * 如果启用“允许组织/组织单位的自定义认证者”选项，但所需认证者仍为空，则会出现 KeyNotFoundException 错误。

### <a name="enhancements"></a>增强功能：

* 泛型 SQL：
  * **方案：重新设计实现：** "*"功能
  * 解决方案说明：更改用于[多值引用属性处理](active-directory-aadconnectsync-connector-genericsql.md)的方法。


### <a name="fixed-issues"></a>已解决的问题：

* 泛型 Web 服务：
  * 如果存在 WebService 连接器，则无法导入服务器配置
  * WebService 连接器无法处理多个 Web 服务

* 泛型 SQL：
  * 没有列出单值引用属性的对象类型
  * 从多值表中删除值时，更改跟踪策略上的增量导入会删除对象
  * GSQL 连接器（DB2 在 AS/400 上）中的 OverflowException

Lotus：
  * 添加了选项，用于在打开 GlobalParameters 页之前启用/禁用搜索 OU

## <a name="114430"></a>1.1.443.0

发布时间：2017 年 3 月

### <a name="enhancements"></a>增强功能

* 泛型 SQL：</br>
  **情景症状：**我们仅允许引用一个对象类型，并要求对成员使用交叉引用，这是一个已知的 SQL 连接器限制。 </br>
  **解决方法说明：**如果选择了“*”选项，在执行引用的处理步骤时，对象类型的所有组合将返回给同步引擎。

>[!Important]
- 这就会创建许多的占位符
- 必须确保命名在对象类型之间保持唯一。


* 泛型 LDAP：</br>
 **情景：**在特定的分区中只选择少量的容器时，搜索仍会针对整个分区执行。 具体的信息由同步服务而不是 MA 筛选，这可能会导致性能下降。 </br>

 **解决方法说明：**更改 GLDAP 连接器的代码，使连接器可通过所有容器并可搜索每个容器中的对象，而不是在整个分区中搜索。


* Lotus Domino：

  **情景：**导出期间用于删除人员的 Domino 邮件删除支持。 </br>
  **解决方法：**导出期间可配置用于删除人员的邮件删除支持。

### <a name="fixed-issues"></a>已解决的问题：
* 泛型 Web 服务：
 * 通过 Web 服务配置工具更改默认 SAP wsconfig 项目中的服务 URL 时发生以下错误：找不到路径的一部分

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* 泛型 LDAP：
 * GLDAP 连接器检测不到 AD LDS 中的所有属性
 * 在 LDAP 目录架构中检测不到 UPN 属性时向导中断
 * 未选择“objectclass”属性时，增量导入失败，但完全导入期间并未出现发现错误
 * “配置分区和层次结构”配置页未显示类型与泛型 LDAP MA 中 Novel 服务器分区类型相同的  
任何对象， 而只显示了 RootDSE 分区中的对象。


* 泛型 SQL：
 * 对无法导入泛型 SQL 水印增量导入多值属性的 Bug 的修复
 * 导出多值属性的已删除/已添加值时，这些值未在数据源中删除/添加。  


* Lotus Notes：
 * 特定字段的“全名”正常显示在 Metaverse 中，但导出到 Notes 时，该属性的值为 Null 或为空。
 * 对重复认证者错误的修复
 * 如果在包含其他对象的 Lotus Domino 连接器中选择不包含任何数据的对象，在执行完全导入时收到发现错误。
 * 在 Lotus Domino 连接器上结束运行增量导入时，Microsoft.IdentityManagement.MA.LotusDomino.Service.exe 服务有时返回应用程序错误。
 * 组成员身份整体上可以正常工作并且会保留，不过，在运行导出以尝试从成员身份中删除某个用户时，会显示更新成功，该用户实际上并未从 Lotus Notes 的成员身份中删除。
 * Lotus MA 的配置 GUI 中添加了一个可将导出模式选择为“在底部追加项”的选项，在导出多值属性期间，可以使用该选项在底部追加新项。
 * 连接器将添加所需的逻辑用于从邮件文件夹和 ID 保管库中删除文件。
 * 删除不适用于跨 NAB 成员的成员身份。
 * 应该从多值属性中成功删除值

## <a name="111170"></a>1.1.117.0
发布时间：2016 年 3 月

**新连接器**  
[泛型 SQL 连接器](active-directory-aadconnectsync-connector-genericsql.md)的初始版本。

**新功能：**

* 通用 LDAP 连接器：
  * 添加了对通过 Isode 进行增量导入的支持。
* Web 服务连接器：
  * 更新了 csEntryChangeResult 活动和 setImportErrorCode 活动，以允许将对象级别错误返回到同步引擎。
  * 更新了 SAP6 和 SAP6User 模板，以使用新的对象级别错误功能。
* Lotus Domino 连接器：
  * 导出时，每个通讯簿需要一个认证者。 现在可以对所有认证者使用同一密码以简化管理。

**已解决的问题：**

* 通用 LDAP 连接器：
  * 对于 IBM Tivoli DS，未正确检测到某些引用属性。
  * 对于增量导入过程中的 Open LDAP，截去了字符串开头和结尾的空格。
  * 对于 Novell 和 NetIQ，导出（在 OU/容器之间移动了对象，同时重命名了对象）失败。
* Web 服务连接器：
  * 如果 Web 服务对于同一绑定具有多个终结点，则连接器未正确发现这些终结点。
* Lotus Domino 连接器：
  * 将 fullName 属性导出到邮件数据库不正常工作。
  * 同时从组中添加和删除成员的导出仅导出了所添加的成员。
  * 如果 Notes Document 无效（isValid 属性设置为 false），则连接器会失败。

## <a name="older-releases"></a>较旧版本
在 2016 年 3 月之前，连接器已发布为支持主题。

**泛型 LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597，2015 年 9 月
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549，2015 年 3 月
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534，2015 年 1 月
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419，2014 年 9 月
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082，2014 年 3 月

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419，2014 年 9 月

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419，2014 年 9 月

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597，2015 年 9 月
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549，2015 年 3 月
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712，2014 年 8 月
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003，2014 年 2 月  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721，2013 年 10 月
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534，2013 年 8 月

## <a name="troubleshooting"></a>故障排除 

> [!NOTE]
> 在更新 Microsoft Identity Manager 或 AADConnect 时使用任意 ECMA2 连接器。 

要满足匹配，必须在升级过程中刷新连接器定义，否则将在应用程序事件日志中接收到如下错误，报表警告 ID 6947：“AAD连接器配置 ("X.X.XXX.X") 中的程序集版本低于 C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll 的实际版本 ("X.X.XXX.X")”。

执行以下操作以刷新定义：
* 打开连接器实例的属性面板
* 单击“连接/连接到”选项卡
  * 输入连接器帐户的密码
* 依次单击每个属性选项卡
  * 如果此连接器类型具有“分区”选项卡，该选项卡中包含“刷新”按钮，请单击该选项卡上的“刷新”按钮
* 在访问过所有属性选项卡后，单击“确认”按钮保存更改。


## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
