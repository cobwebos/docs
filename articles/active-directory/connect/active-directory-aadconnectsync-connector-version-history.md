---
title: "连接器版本发行历史记录 | Microsoft Docs"
description: "本主题列出了 Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的连接器的所有版本"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 244ca634cfd47ee37e3845380ac05dc68d406621
ms.lasthandoff: 03/30/2017


---
# <a name="connector-version-release-history"></a>连接器版本发行历史记录
Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的连接器会经常更新。

> [!NOTE]
> 本主题仅适用于 FIM 和 MIM。 Azure AD Connect 不支持这些连接器。

本主题列出所有已发布的连接器版本。

相关链接：

* [下载最新连接器](http://go.microsoft.com/fwlink/?LinkId=717495)
* [泛型 LDAP 连接器](active-directory-aadconnectsync-connector-genericldap.md)参考文档
* [泛型 SQL 连接器](active-directory-aadconnectsync-connector-genericsql.md)参考文档
* [Web 服务连接器](http://go.microsoft.com/fwlink/?LinkID=226245)参考文档
* [PowerShell 连接器](active-directory-aadconnectsync-connector-powershell.md)参考文档
* [Lotus Domino 连接器](active-directory-aadconnectsync-connector-domino.md)参考文档

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
  **解决方法：**导出期间可配置用于删除人员的 Domino 邮件删除支持。

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
 * 组成员身份整体上可以正常工作并且会保留，不过，在运行导出以尝试从成员身份中删除某个用户时，将显示更新成功，该用户实际上并未从 Lotus Notes 的成员身份中删除。
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
  * 如果 Notes Document 无效（isValid 属性设置为 false），则连接器将失败。

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

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。

