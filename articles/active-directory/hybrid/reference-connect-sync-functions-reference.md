---
title: Azure AD Connect 同步：函数引用 | Microsoft Docs
description: 在 Azure AD Connect 同步中引用声明性设置表达式。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900045"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect 同步：函数参考
在 Azure AD Connect 中，函数用于在同步期间操作属性值。  
函数的语法使用以下格式表示：  
`<output type> FunctionName(<input type> <position name>, ..)`

如果函数被重载并接受多个语法，则会列出所有的有效语法。  
该函数为强类型函数，并会验证传递的类型是否匹配记录的类型。  
如果类型不匹配，将引发错误。

类型使用以下语法表示：

* **bin** - 二进制
* **bool** - 布尔值
* **dt** - UTC 日期/时间
* **enum** - 已知常量的枚举
* **exp** - 表达式，它的计算结果应当是一个布尔值
* **mvbin** – 多值二进制
* **mvstr** – 多值字符串
* **mvref** – 多值引用
* **num** - 数字
* **ref** – 引用
* **str** – 字符串
* **var** -（几乎）任何其他类型的一个变体
* **void** - 不返回值

**mvbin**、**mvstr** 和 **mvref** 类型的函数只适用于多值属性。 **bin**、**str** 和 **ref** 类型的函数只适用于单值和多值属性。

## <a name="functions-reference"></a>函数引用

| 函数列表 |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certificate** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **转换** | | | | |
| [CBool](#cbool) |[Cdate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **日期/时间** | | | | |
| [日期添加](#dateadd) |[DateFromNum](#datefromnum) |[格式日期时间](#formatdatetime) |[立即](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **目录** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **计算** | | | | |
| [IsBitSet](#isbitset) |[是日期](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Math** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Multi-valued** | | | | |
| [包含](#contains) |[计数](#count) |[项目](#item) |[ItemOrNull](#itemornull) | |
| [联接](#join) |[RemoveDuplicates](#removeduplicates) |[分裂](#split) | | |
| **Program Flow** | | | | |
| [错误](#error) |[Iif](#iif) |[选择](#select) |[开关](#switch) | |
| [哪里](#where) |[与](#with) | | | |
| **Text** | | | | |
| [Guid](#guid) |[因斯特](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [离开](#left) |[莱恩](#len) |[L特里姆](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[取代](#replace) | |
| [ReplaceChars](#replacechars) |[Right](#right) |[RTrim](#rtrim) |[修剪](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>BitAnd
**描述:**  
BitAnd 函数设置值的指定位。

**语法：**  
`num BitAnd(num value1, num value2)`

* value1、value2：应该使用 AND 联接在一起的数字值

**言论：**  
此函数将两个参数转换为二进制表示形式，并将位设置为：

* 0 - 如果 *value1* 和 *value2* 中的一个或两个对应位为 0
* 1 - 如果两个相应位均为 1。

换而言之，除了当两个参数的相应位均为 1 时之外，所有情况下均返回 0。

**例子：**  
`BitAnd(&HF, &HF7)`  
返回 7，因为十六进制 "F" AND "F7" 的计算结果为此值。

---
### <a name="bitor"></a>BitOr
**描述:**  
BitOr 函数设置值的指定位。

**语法：**  
`num BitOr(num value1, num value2)`

* value1、value2：应该 OR 在一起的数字值

**言论：**  
此函数将两个参数转换为二进制表示形式，当掩码和标志中相应位的其中一个或两个均为 1 时，将位设置为 1，当两个相应位均为 0 时，设置为 0。 换而言之，除了当两个参数的相应位均为 0 时之外，所有情况下均返回 1。

---
### <a name="cbool"></a>CBool
**描述:**  
CBool 函数基于计算的表达式返回布尔值

**语法：**  
`bool CBool(exp Expression)`

**言论：**  
如果表达式的计算结果为非零值，则 CBool 返回 True，否则返回 False。

**例子：**  
`CBool([attrib1] = [attrib2])`  

如果两个属性具有相同的值，则返回 True。

---
### <a name="cdate"></a>Cdate
**描述:**  
CDate 函数通过字符串返回 UTC DateTime。 DateTime 不是 Sync 中的原生属性类型，但被某些函数使用。

**语法：**  
`dt CDate(str value)`

* Value：具有日期、时间和可选时区的字符串

**言论：**  
返回的字符串始终采用 UTC 格式。

**例子：**  
`CDate([employeeStartTime])`  
基于员工的开始时间返回 DateTime

`CDate("2013-01-10 4:00 PM -8")`  
返回表示“2013-01-11 12:00 AM”的 DateTime


---
### <a name="certextensionoids"></a>CertExtensionOids
**描述:**  
返回证书对象所有关键扩展的 Oid 值。

**语法：**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certformat"></a>CertFormat
**描述:**  
返回此 X.509v3 证书的格式名称。

**语法：**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certfriendlyname"></a>CertFriendlyName
**描述:**  
返回证书的关联别名。

**语法：**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certhashstring"></a>CertHashString
**描述:**  
以十六进制字符串的形式返回 X.509v3 证书的 SHA1 哈希值。

**语法：**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certissuer"></a>CertIssuer
**描述:**  
返回颁发此 X.509v3 证书的证书颁发机构名称。

**语法：**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certissuerdn"></a>CertIssuerDN
**描述:**  
返回证书颁发者的可分辨名称。

**语法：**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certissueroid"></a>CertIssuerOid
**描述:**  
返回证书颁发者的 Oid。

**语法：**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**描述:**  
以字符串形式返回此 X.509v3 证书的密钥算法信息。

**语法：**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**描述:**  
以十六进制字符串的形式返回此 X.509v3 证书的密钥算法参数。

**语法：**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certnameinfo"></a>CertNameInfo
**描述:**  
返回证书中的使用者和颁发者名称。

**语法：**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。
*   X509NameType：针对使用者的 X509NameType 值。
*   includesIssuerName：包含颁发者名称则为 true；否则为 false。

---
### <a name="certnotafter"></a>CertNotAfter
**描述:**  
返回证书不再有效后的本地时间日期。

**语法：**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certnotbefore"></a>CertNotBefore
**描述:**  
返回证书开始生效的本地时间日期。

**语法：**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**描述:**  
返回此 X.509v3 证书的公钥 Oid。

**语法：**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**描述:**  
返回此 X.509v3 证书的公钥参数 Oid。

**语法：**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certserialnumber"></a>CertSerialNumber
**描述:**  
返回 X.509v3 证书的序列号。

**语法：**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**描述:**  
返回用于创建证书签名的算法 Oid。

**语法：**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certsubject"></a>CertSubject
**描述:**  
获取证书中使用者的可分辨名称。

**语法：**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**描述:**  
返回证书中使用者的可分辨名称。

**语法：**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**描述:**  
返回证书中使用者名称的 Oid。

**语法：**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certthumbprint"></a>CertThumbprint
**描述:**  
返回证书的指纹。

**语法：**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="certversion"></a>CertVersion
**描述:**  
返回证书的 X.509 格式版本。

**语法：**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。

---
### <a name="cguid"></a>CGuid
**描述:**  
CGuid 函数将 GUID 的字符串表示转换为其二进制表示形式。

**语法：**  
`bin CGuid(str GUID)`

* 采用这种模式设置格式的字符串：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>包含
**描述:**  
Contains 函数查找多值属性内的字符串

**语法：**  
`num Contains (mvstring attribute, str search)` - 区分大小写  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` - 区分大小写

* attribute：要搜索的多值属性。
* search：在属性中查找的字符串。
* Casetype：CaseInsensitive 或 CaseSensitive。

返回找到字符串的多值属性中的索引。 如果未找到字符串，则返回 0。

**言论：**  
对于多值字符串属性，搜索会在值中查找子字符串。  
对于引用属性，搜索的字符串必须与视为匹配的值完全匹配。

**例子：**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
如果 proxyAddresses 属性具有主电子邮件地址（由大写 "SMTP:" 表示），则返回 proxyAddress 属性，否则返回错误。

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**描述:**  
ConvertFromBase64 函数将指定的 base64 编码值转换为规则的字符串。

**语法：**  
`str ConvertFromBase64(str source)` - 假设采用 Unicode 编码  
`str ConvertFromBase64(str source, enum Encoding)`

* source：Base64 编码的字符串  
* Encoding：Unicode、ASCII、UTF8

**示例**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

这两个示例均返回 "*Hello world!*"

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**描述:**  
ConvertFromUTF8Hex 函数将指定的 UTF8 Hex 编码值转换为字符串。

**语法：**  
`str ConvertFromUTF8Hex(str source)`

* source：UTF8 2 字节编码的字符串

**言论：**  
该结果中此函数和 ConvertFromBase64([],UTF8) 之间的差异对 DN 属性来说是可以接受的。  
此格式被 Azure Active Directory 用作 DN。

**例子：**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
返回 "*Hello world!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**描述:**  
ConvertToBase64 函数将字符串转换为 Unicode base64 字符串。  
将整数数组的值转换为其等效字符串表示形式，该表示形式使用 base 64 数字编码。

**语法：**  
`str ConvertToBase64(str source)`

**例子：**  
`ConvertToBase64("Hello world!")`  
返回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**描述:**  
ConvertToUTF8Hex 函数将字符串转换为 UTF8 Hex 编码的值。

**语法：**  
`str ConvertToUTF8Hex(str source)`

**言论：**  
此函数的输出格式被 Azure Active Directory 用作 DN 属性。

**例子：**  
`ConvertToUTF8Hex("Hello world!")`  
返回 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**描述:**  
Count 函数返回多值属性中的元素数量

**语法：**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**描述:**  
CNum 函数使用字符串并返回数值数据类型。

**语法：**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**描述:**  
将字符串转换为引用属性

**语法：**  
`ref CRef(str value)`

**例子：**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**描述:**  
CStr 函数转换为字符串数据类型。

**语法：**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value：可以是数字值、引用属性或布尔值。

**例子：**  
`CStr([dn])`  
可能返回 "cn=Joe,dc=contoso,dc=com"

---
### <a name="dateadd"></a>DateAdd
**描述:**  
返回日期，其中包含已添加制定时间间隔的日期。

**语法：**  
`dt DateAdd(str interval, num value, dt date)`

* 间隔：表示要添加的时间间隔的字符串表达式。 字符串必须具有下列值之一：
  * yyyy Year
  * q Quarter
  * m Month
  * y Day of year
  * d Day
  * w Weekday
  * ww Week
  * h Hour
  * n Minute
  * s Second
* 值：要添加的单位数。 它可以是正值（以获取将来的日期）或负值（以获取过去的日期）。
* 日期：表示间隔添加到其中的日期的 DateTime。

**例子：**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
添加 3 个月，并返回表示“2001-04-01”的 DateTime。

---
### <a name="datefromnum"></a>DateFromNum
**描述:**  
DateFromNum 函数将 AD 的日期格式的值转换为 DateTime 类型。

**语法：**  
`dt DateFromNum(num value)`

**例子：**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
返回表示 2012-01-01 23:00:00 的 DateTime

---
### <a name="dncomponent"></a>DNComponent
**描述:**  
DNComponent 函数返回从左边起的指定 DN 组件的值。

**语法：**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn：要解释的引用属性
* ComponentNumber：要返回的 DN 中的组件

**例子：**  
`DNComponent(CRef([dn]),1)`  
如果 dn 为“cn=Joe,ou=…”，则返回 Joe

---
### <a name="dncomponentrev"></a>DNComponentRev
**描述:**  
DNComponentRev 函数返回从右边起（末尾）的指定 DN 组件的值。

**语法：**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn：要解释的引用属性
* ComponentNumber - 要返回的 DN 中的组件
* Options：DC – 忽略具有“dc=”的所有组件

**例子：**  
如果 dn 为 "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com"，则  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
两者都返回 US。

---
### <a name="error"></a>错误
**描述:**  
Error 函数用于返回自定义错误。

**语法：**  
`void Error(str ErrorMessage)`

**例子：**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
如果属性 accountName 不存在，则对象上引发错误。

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**描述:**  
EscapeDNComponent 函数使用 DN 的一个组件，并对其进行转义，以便它可以在 LDAP 中表示。

**语法：**  
`str EscapeDNComponent(str value)`

**例子：**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
即使 displayName 属性具有必须在 LDAP 中转义的字符，也请确保可以在 LDAP 目录中创建对象。

---
### <a name="formatdatetime"></a>FormatDateTime
**描述:**  
FormatDateTime 函数用于为具有指定格式的字符串设置 DateTime 格式

**语法：**  
`str FormatDateTime(dt value, str format)`

* value：DateTime 格式的值
* format：表示要转换为的格式的字符串。

**言论：**  
格式的可能值可以在这里找到[：FORMAT 函数的自定义日期和时间格式](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function)。

**例子：**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
结果是“2007-12-25”。

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
结果可能是“20140905081453.0Z”

---
### <a name="guid"></a>Guid
**描述:**  
函数 GUID 生成新的随机 GUID

**语法：**  
`str Guid()`

---
### <a name="iif"></a>IIF
**描述:**  
IIF 函数基于指定的条件返回一组可能值中的其中一个值。

**语法：**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition：计算结果可能为 true 或 false 的任何值或表达式。
* valueIfTrue：如果条件计算结果为 true，则为返回值。
* valueIfFalse：如果条件计算结果为 false，则为返回值。

**例子：**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 如果用户是实习生，则返回用户的别名，同时将 "t-" 添加到其开头，否则按原样返回用户的别名。

---
### <a name="instr"></a>InStr
**描述:**  
InStr 函数查找字符串中第一次出现的子字符串

**语法：**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck：要搜索的字符串
* stringmatch：要查找的字符串
* start：查找子字符串的起始位置
* compare：vbTextCompare 或 vbBinaryCompare

**言论：**  
返回其中已找到子字符串的位置，如果未找到，则返回 0。

**例子：**  
`InStr("The quick brown fox","quick")`  
计算结果为 5

`InStr("repEated","e",3,vbBinaryCompare)`  
计算结果为 7

---
### <a name="instrrev"></a>InStrRev
**描述:**  
InStrRev 函数查找字符串中最后一次出现的子字符串

**语法：**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck：要搜索的字符串
* stringmatch：要查找的字符串
* start：查找子字符串的起始位置
* compare：vbTextCompare 或 vbBinaryCompare

**言论：**  
返回其中已找到子字符串的位置，如果未找到，则返回 0。

**例子：**  
`InStrRev("abbcdbbbef","bb")`  
返回 7

---
### <a name="isbitset"></a>IsBitSet
**描述:**  
函数 IsBitSet 测试是否设置了位

**语法：**  
`bool IsBitSet(num value, num flag)`

* value：计算的数字值。标志：表示具有要计算的位的数字值

**例子：**  
`IsBitSet(&HF,4)`  
返回 True，因为位“4”在十六进制值“F”中设置

---
### <a name="isdate"></a>IsDate
**描述:**  
如果表达式可以计算为 DateTime 类型，则 IsDate 函数计算结果为 True。

**语法：**  
`bool IsDate(var Expression)`

**言论：**  
用来确定 CDate() 是否成功。

---
### <a name="iscert"></a>IsCert
**描述:**  
如果原始数据可以序列化为 .NET X509Certificate2 证书对象，则返回 true。

**语法：**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData：X.509 证书的字节数组表示形式。 字节数组可以是编码的二进制文件 (DER) 或 Base64 编码的 X.509 数据。
---
### <a name="isempty"></a>IsEmpty
**描述:**  
如果属性出现在 CS 或 MV 中，但计算结果为空字符串，则 IsEmpty 函数计算结果为 True。

**语法：**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**描述:**  
如果字符串可以转换为 GUID，则 IsGuid 函数计算结果为 true。

**语法：**  
`bool IsGuid(str GUID)`

**言论：**  
GUID 定义为遵循以下其中一种模式的字符串：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

用来确定 CGuid() 是否成功。

**例子：**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
如果 StrAttribute 具有 GUID 格式，则返回二进制表示形式，否则返回 Null。

---
### <a name="isnull"></a>IsNull
**描述:**  
如果表达式的计算结果为 Null，则 IsNull 函数返回 true。

**语法：**  
`bool IsNull(var Expression)`

**言论：**  
对于属性，Null 表示缺少属性。

**例子：**  
`IsNull([displayName])`  
如果属性没有在 CS 或 MV 中出现，则返回 True。

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**描述:**  
如果表达式为 null 或空字符串，则 IsNullOrEmpty 函数返回 true。

**语法：**  
`bool IsNullOrEmpty(var Expression)`

**言论：**  
对于属性，如果属性不存在，或存在但为空字符串，此语法计算结果则为 True。  
此函数的逆函数被命名为 IsPresent。

**例子：**  
`IsNullOrEmpty([displayName])`  
如果属性在 CS 或 MV 中没有出现或为空字符串，则返回 True。

---
### <a name="isnumeric"></a>IsNumeric
**描述:**  
IsNumeric 函数返回布尔值，该值指示表达式是否可以计算为数字类型。

**语法：**  
`bool IsNumeric(var Expression)`

**言论：**  
用来确定 CNum() 是否能成功分析表达式。

---
### <a name="isstring"></a>IsString
**描述:**  
如果表达式可以计算为字符串类型，则 IsString 函数计算结果为 True。

**语法：**  
`bool IsString(var expression)`

**言论：**  
用来确定 CStr() 是否能成功分析表达式。

---
### <a name="ispresent"></a>IsPresent
**描述:**  
如果表达式的计算结果为字符串，该字符串不是 Null 且不为空，则 IsPresent 函数返回 true。

**语法：**  
`bool IsPresent(var expression)`

**言论：**  
此函数的逆函数被命名为 IsNullOrEmpty。

**例子：**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**描述:**  
Item 函数返回多值字符串/属性中的一个项。

**语法：**  
`var Item(mvstr attribute, num index)`

* attribute：多值属性
* index：对多值字符串中某个项的索引。

**言论：**  
Item 函数与 Contains 函数一起使用很有利，因为后一函数返回对多值属性中某个项的索引。

如果索引超出界限，则引发错误。

**例子：**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
返回主电子邮件地址。

---
### <a name="itemornull"></a>ItemOrNull
**描述:**  
ItemOrNull 函数返回多值字符串/属性中的一个项。

**语法：**  
`var ItemOrNull(mvstr attribute, num index)`

* attribute：多值属性
* index：对多值字符串中某个项的索引。

**言论：**  
ItemOrNull 函数与 Contains 函数一起使用很有利，因为后一函数返回对多值属性中某个项的索引。

如果索引超出界限，则返回 Null 值。

---
### <a name="join"></a>联接
**描述:**  
Join 函数使用多值字符串，并返回每个项之间插入指定分隔符的单值字符串。

**语法：**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribute：包含要联接的字符串的多值属性。
* delimiter：任意字符串，用于分隔返回的字符串中的子字符串。 如果省略，则使用空格字符（“ ”）。 如果分隔符为零长度字符串（“”）或零，则列表中的所有项都不使用分隔符连接。

**备注**  
Join 和 Split 函数之间没有奇偶校验。 Join 函数使用字符串数组，并使用分隔符字符串将它们联接起来，以返回单个字符串。 Split 函数使用字符串并以分隔符分隔，以返回字符串数组。 但是，主要区别是 Join 可以使用任何分隔符字符串将字符串连接起来，而 Split 仅可以使用单个字符分隔符分隔字符串。

**例子：**  
`Join([proxyAddresses],",")`  
可能返回：“SMTP:john.doe@contoso.com、smtp:jd@contoso.com”

---
### <a name="lcase"></a>LCase
**描述:**  
LCase 函数将字符串中的所有字符都转换为小写。

**语法：**  
`str LCase(str value)`

**例子：**  
`LCase("TeSt")`  
返回“Test”。

---
### <a name="left"></a>Left
**描述:**  
Left 函数从字符串左侧起返回指定的字符数。

**语法：**  
`str Left(str string, num NumChars)`

* string：从中返回字符的字符串
* NumChar：标识从字符串开头（左侧）起返回的字符数的数字

**言论：**  
包含字符串中第一个 numChars 字符的字符串：

* 如果 numChar = 0，则返回空字符串。
* 如果 numChar < 0，则返回输入字符串。
* 如果字符串为 null，则返回空字符串。

如果字符串包含的字符数比 numChars 中指定的数量少，则返回与该字符串相同的字符串（即，包含参数 1 中的所有字符）。

**例子：**  
`Left("John Doe", 3)`  
返回“Joh”。

---
### <a name="len"></a>Len
**描述:**  
Len 函数返回字符串中的字符数。

**语法：**  
`num Len(str value)`

**例子：**  
`Len("John Doe")`  
返回 8

---
### <a name="ltrim"></a>LTrim
**描述:**  
LTrim 函数从字符串中删除前导空格。

**语法：**  
`str LTrim(str value)`

**例子：**  
`LTrim(" Test ")`  
返回“Test”

---
### <a name="mid"></a>Mid
**描述:**  
Mid 函数从字符串指定位置起返回指定的字符数。

**语法：**  
`str Mid(str string, num start, num NumChars)`

* string：从中返回字符的字符串
* start：标识从中返回字符的字符串中起始位置的数字
* NumChar：标识从字符串中的位置返回的字符数的数字

**言论：**  
从字符串中的开始位置开始返回 numChars 字符。  
包含字符串中开始位置的 numChar 字符的字符串：

* 如果 numChar = 0，则返回空字符串。
* 如果 numChar < 0，则返回输入字符串。
* 如果 start > 字符串的长度，则返回输入字符串。
* 如果 start <= 0，则返回输入字符串。
* 如果字符串为 null，则返回空字符串。

如果字符串中开始位置没有保留的 numChar 字符，则返回尽可能多的字符。

**例子：**  
`Mid("John Doe", 3, 5)`  
返回“hn Do”。

`Mid("John Doe", 6, 999)`  
返回“Doe”

---
### <a name="now"></a>现在
**描述:**  
Now 函数根据计算机的系统日期和时间返回指定当前日期和时间的 DateTime。

**语法：**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**描述:**  
NumFromDate 函数返回 AD 的日期格式的日期。

**语法：**  
`num NumFromDate(dt value)`

**例子：**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
返回 129699324000000000

---
### <a name="padleft"></a>PadLeft
**描述:**  
PadLeft 函数使用提供的填充字符将字符串从左侧填充到指定长度。

**语法：**  
`str PadLeft(str string, num length, str padCharacter)`

* string：要填充的字符串。
* length：表示所需字符串长度的整数。
* padCharacter：包含用作填充字符的单个字符的字符串

**言论：**

* 如果字符串的长度小于 length，则 padCharacter 会重复追加到字符串的开头（左侧），直到其长度等于 length。
* PadCharacter 可以是空格字符，但不能为 null 值。
* 如果字符串的长度等于或大于 length，则返回不变的字符串。
* 如果字符串的长度大于或等于 length，则返回与 string 相同的字符串。
* 如果字符串的长度小于 length，则返回具有所需长度的新字符串，其中包含用 padCharacter 填充的字符串。
* 如果字符串为 null，该函数则返回空字符串。

**例子：**  
`PadLeft("User", 10, "0")`  
返回“000000User”。

---
### <a name="padright"></a>PadRight
**描述:**  
PadRight 函数使用提供的填充字符将字符串从右侧填充到指定长度。

**语法：**  
`str PadRight(str string, num length, str padCharacter)`

* string：要填充的字符串。
* length：表示所需字符串长度的整数。
* padCharacter：包含用作填充字符的单个字符的字符串

**言论：**

* 如果字符串的长度小于 length，则 padCharacter 会重复追加到字符串的末尾（右侧），直到其长度等于 length。
* padCharacter 可以是空格字符，但不能为 null 值。
* 如果字符串的长度等于或大于 length，则返回不变的字符串。
* 如果字符串的长度大于或等于 length，则返回与 string 相同的字符串。
* 如果字符串的长度小于 length，则返回具有所需长度的新字符串，其中包含用 padCharacter 填充的字符串。
* 如果字符串为 null，该函数则返回空字符串。

**例子：**  
`PadRight("User", 10, "0")`  
返回“User000000”。

---
### <a name="pcase"></a>PCase
**描述:**  
PCase 函数将字符串中每个空格分隔词的第一个字符转换为大写形式，并将所有其他字符都转换为小写形式。

**语法：**  
`String PCase(string)`

**言论：**

* 此函数目前无法正常转换全大写单词（例如首字母缩写词）的大小写。

**例子：**  
`PCase("TEsT")`  
返回“Test”。

`PCase(LCase("TEST"))`  
返回“Test”

---
### <a name="randomnum"></a>RandomNum
**描述:**  
RandomNum 函数返回指定间隔之间的随机数字。

**语法：**  
`num RandomNum(num start, num end)`

* start：标识要生成的随机值的下限的数字
* end：标识要生成的随机值的上限的数字

**例子：**  
`Random(100,999)`  
可能返回 734。

---
### <a name="removeduplicates"></a>RemoveDuplicates
**描述:**  
RemoveDuplicates 函数使用多值字符串，并确保每个值都是唯一值。

**语法：**  
`mvstr RemoveDuplicates(mvstr attribute)`

**例子：**  
`RemoveDuplicates([proxyAddresses])`  
返回净化的 proxyAddress 属性，其中所有重复值已被删除。

---
### <a name="replace"></a>将
**描述:**  
Replace 函数将所有出现的某一字符串替换为另一个字符串。

**语法：**  
`str Replace(str string, str OldValue, str NewValue)`

* string：替换其中的值的字符串。
* OldValue：要搜索和替换的字符串。
* NewValue：要替换的字符串。

**言论：**  
该函数可以识别以下特殊 moniker：

* \n – 新行
* \r – 回车符
* \t – 选项卡

**例子：**  
`Replace([address],"\r\n",", ")`  
将 CRLF 替换为逗号和空格，可能导致出现“One Microsoft Way, Redmond, WA, USA”

---
### <a name="replacechars"></a>ReplaceChars
**描述:**  
ReplaceChars 函数替换 ReplacePattern 字符串中找到的所有出现的字符。

**语法：**  
`str ReplaceChars(str string, str ReplacePattern)`

* string：替换其中值的字符串。
* ReplacePattern：包含具有要替换字符的字典的字符串。

格式为 {source1}:{target1},{source2}:{target2},{sourceN},{targetN}，其中源是要查找并确定要替换的目标字符串的字符。

**言论：**

* 该函数使用每次出现的定义的源，并使用目标替换它们。
* 源必须正好是一个 (unicode) 字符。
* 源不能为空或长度超过一个字符（分析错误）。
* 目标可以有多个字符，例如 ö:oe、β:ss。
* 目标可以为空，该值指示应删除字符。
* 源区分大小写，并且必须是完全匹配。
* 逗号 (,) 和冒号 (:) 是保留的字符，不能使用此函数进行替换。
* 空格和 ReplacePattern 字符串中的其他空白字符被忽略。

**例子：**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
返回 Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
返回“ONeil”，定义要删除单次勾选。

---
### <a name="right"></a>Right
**描述:**  
Right 函数从字符串右侧（末尾）起返回指定的字符数。

**语法：**  
`str Right(str string, num NumChars)`

* string：从中返回字符的字符串
* NumChar：标识从字符串末尾（右侧）起返回的字符数的数字

**言论：**  
NumChars 字符从字符串的最后位置返回。

包含字符串中最后的 numChar 字符的字符串：

* 如果 numChar = 0，则返回空字符串。
* 如果 numChar < 0，则返回输入字符串。
* 如果字符串为 null，则返回空字符串。

如果字符串包含的字符数比 NumChar 中指定的数量少，则返回与该字符串相同的字符串。

**例子：**  
`Right("John Doe", 3)`  
返回“Doe”。

---
### <a name="rtrim"></a>RTrim
**描述:**  
RTrim 函数从字符串中删除尾随空格。

**语法：**  
`str RTrim(str value)`

**例子：**  
`RTrim(" Test ")`  
返回“Test”。

---
### <a name="select"></a>Select
**描述:**  
根据指定函数处理多值属性（或表达式输出）中的所有值。

**语法：**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item：表示多值属性中的元素
* attribute：多值属性
* expression：一个返回值集合的表达式
* condition：可以处理属性中某个项的任何函数

**例子：**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
在删除连字符 (-) 后，返回多值属性 otherPhone 中的所有值。

---
### <a name="split"></a>拆分
**描述:**  
Split 函数使用采用分隔符分隔的字符串，并使其成为多值字符串。

**语法：**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* value：用分隔符字符来分隔的字符串。
* delimiter：用作分隔符的单个字符。
* limit：可返回的最大数目的值。

**例子：**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
返回多值字符串，其中 2 个元素对 proxyAddress 属性有用。

---
### <a name="stringfromguid"></a>StringFromGuid
**描述:**  
StringFromGuid 函数使用二进制 GUID，并将其转换为字符串

**语法：**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**描述:**  
StringFromSid 函数将包含安全标识符的字节数组转换为字符串。

**语法：**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>开关
**描述:**  
Switch 函数用于基于计算的条件返回单个值。

**语法：**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr：想要计算结果的变体表达式。
* value：当相应表达式为 True 时要返回的值。

**言论：**  
Switch 函数参数列表包含表达式和值对。 表达式从左到右计算结果，并返回与计算结果为 True 的第一个表达式相关联的值。 如果没有正确配对部件，则会发生运行时错误。

例如，如果 expr1 为 True，则 Switch 返回 value1。 如果 expr-1 为 False，但 expr-2 为 True，则 Switch 返回 value-2，依此类推。

如果满足以下条件，Switch 将返回 Nothing：

* 没有任何表达式求值为 True。
* 第一个 True 表达式的相应值为 Null。

Switch 对所有表达式求值，即使它只返回其中一个结果。 为此，应监视非预期的负面影响。 例如，如果任何表达式的计算结果导致除数为零的错误，则会出现错误。

值还可以是将返回自定义字符串的错误函数。

**例子：**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
返回某些主要城市所使用的语言，否则返回错误。

---
### <a name="trim"></a>Trim
**描述:**  
Trim 函数从字符串中删除前导空格和尾随空格。

**语法：**  
`str Trim(str value)`  

**例子：**  
`Trim(" Test ")`  
返回“Test”。

`Trim([proxyAddresses])`  
删除 proxyAddress 属性中每个值的前导空格和尾随空格。

---
### <a name="ucase"></a>UCase
**描述:**  
UCase 函数将字符串中的所有字符都转换为大写形式。

**语法：**  
`str UCase(str string)`

**例子：**  
`UCase("TeSt")`  
返回“TEST”。

---
### <a name="where"></a>其中

**描述:**  
根据指定条件，从多值属性（或表达式输出）中返回值子集。

**语法：**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item：表示多值属性中的元素
* attribute：多值属性
* condition：计算结果可能为 true 或 false 的任何表达式
* expression：一个返回值集合的表达式

**例子：**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
返回多值属性 userCertificate 中未过期的证书值。

---
### <a name="with"></a>With
**描述:**  
通过使用变量来表示在复杂表达式中出现一次或多次的子表达式，With 函数提供了一种简化复杂表达式的方法。

**语法：**
`With(var variable, exp subExpression, exp complexExpression)`  
* variable：表示子表达式。
* subExpression：由变量表示的子表达式。
* complexExpression：复杂表达式。

**例子：**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
在功能上等效于：  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
它在 userCertificate 属性中仅返回未过期的证书值。


---
### <a name="word"></a>Word
**描述:**  
基于描述要使用的分隔符与要返回的单词数的参数，Word 函数返回字符串中包含的单词。

**语法：**  
`str Word(str string, num WordNumber, str delimiters)`

* string：从中返回单词的字符串。
* WordNumber：标识应返回单词数的数字。
* delimiter：表示应该用于标识单词的分隔符的字符串

**言论：**  
字符串中的字符由分隔符中其中一个字符分隔的每个字符串被标识为单词：

* 如果数字 < 1，则返回空字符串。
* 如果字符串为 null，则返回空字符串。

如果字符串包含的单词少于应返回数字或字符串不包含由分隔符标识的任何单词，则返回空字符串。

**例子：**  
`Word("The quick brown fox",3," ")`  
返回“brown”

`Word("This,string!has&many separators",3,",!&#")`  
返回“has”

## <a name="additional-resources"></a>其他资源
* [了解声明性预配表达式](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD 连接同步：自定义同步选项](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure 活动目录集成](whatis-hybrid-identity.md)
