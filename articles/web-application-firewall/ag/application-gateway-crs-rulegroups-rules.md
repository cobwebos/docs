---
title: CRS 规则组和规则
titleSuffix: Azure Web Application Firewall
description: 本页提供有关 Web 应用程序防火墙 CRS 规则组和规则的信息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 0303f09e5e704a18576bf50d1f00007f7f86f320
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279242"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Web 应用程序防火墙 CRS 规则组和规则

出现常见的漏洞和攻击时，应用程序网关 Web 应用程序防火墙 (WAF) 可保护 Web 应用程序。 这是通过基于 OWASP 核心规则集3.1、3.0 或2.2.9 定义的规则来完成的。 可以逐个规则地禁用这些规则。 本文包含当前提供的规则和规则集。

## <a name="core-rule-sets"></a>核心规则集

默认情况下，应用程序网关 WAF 是由 CRS 3.0 预配置的。 但你可以改为选择使用 CRS 3.1 或 CRS 2.2.9。 CRS 3.1 提供了新的规则集来防范 Java 感染、一组初始文件上传检查、纠正误报等等。 CRS 3.0 比 CRS 2.2.9 减少了误报。 你还可以[自定义规则以满足你的需求](application-gateway-customize-waf-rules-portal.md)。

> [!div class="mx-imgBorder"]
> ![管理规则](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF 可防止以下 web 漏洞：

- SQL 注入式攻击
- 跨站点脚本攻击
- 其他常见攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含
- HTTP 协议冲突
- HTTP 协议异常，如缺少主机用户代理和接受标头
- Bot、爬网程序和扫描程序
- 常见的应用程序配置错误（例如 Apache 和 IIS）

### <a name="owasp-crs-31"></a>OWASP CRS 3。1

CRS 3.1 包含13个规则组，如下表所示。 每个组都包含多个可以禁用的规则。

|规则组|说明|
|---|---|
|**[常规](#general-31)**|常规组|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|锁定方法（PUT、PATCH）|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|防范端口和环境扫描程序|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|防范协议和编码问题|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|防止标头注入、请求走私和响应拆分|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|防范文件和路径攻击|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|防范远程文件包含（RFI）攻击|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|再次保护远程代码执行攻击|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|防范 PHP 注入式攻击|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|抵御跨站点脚本攻击|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|防范 SQL 注入式攻击|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|抵御会话固定攻击|
|**[请求-944-应用程序-会话-JAVA](#crs944-31)**|防范 JAVA 攻击|

### <a name="owasp-crs-30"></a>OWASP CRS 3。0

CRS 3.0 包括12个规则组，如下表所示。 每个组都包含多个可以禁用的规则。

|规则组|说明|
|---|---|
|**[常规](#general-30)**|常规组|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|锁定方法（PUT、PATCH）|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|防范端口和环境扫描程序|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|防范协议和编码问题|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|防止标头注入、请求走私和响应拆分|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|防范文件和路径攻击|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|防范远程文件包含（RFI）攻击|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|再次保护远程代码执行攻击|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|防范 PHP 注入式攻击|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|抵御跨站点脚本攻击|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|防范 SQL 注入式攻击|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|抵御会话固定攻击|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2。9

CRS 2.2.9 包含10个规则组，如下表所示。 每个组都包含多个可以禁用的规则。

|规则组|说明|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|防范协议冲突（如无效字符或带有请求正文的 GET）|
|**[crs_21_protocol_anomalies](#crs21)**|防范错误的标头信息|
|**[crs_23_request_limits](#crs23)**|防范超出限制的参数或文件|
|**[crs_30_http_policy](#crs30)**|防范受限方法、标头和文件类型|
|**[crs_35_bad_robots](#crs35)**|防范 web 爬网程序和扫描程序|
|**[crs_40_generic_attacks](#crs40)**|防范一般攻击（例如会话固定、远程文件包含和 PHP 注入）|
|**[crs_41_sql_injection_attacks](#crs41sql)**|防范 SQL 注入式攻击|
|**[crs_41_xss_attacks](#crs41xss)**|抵御跨站点脚本攻击|
|**[crs_42_tight_security](#crs42)**|防范路径遍历攻击|
|**[crs_45_trojans](#crs45)**|防范后门特洛伊木马|

在应用程序网关上使用 Web 应用程序防火墙时，可以使用以下规则组和规则。

# <a name="owasp-31"></a>[OWASP 3。1](#tab/owasp31)

## <a name="owasp31"></a>规则集

### <a name="general-31"></a> <p x-ms-format-detection="none">常规</p>

|RuleId|说明|
|---|---|
|200004|可能的多部分不匹配边界。|

### <a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|说明|
|---|---|
|911100|方法不受策略允许|


### <a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|说明|
|---|---|
|913100|找到了与安全扫描程序关联的用户代理|
|913101|找到了与脚本/通用 HTTP 客户端关联的用户代理|
|913102|找到了与 Web 爬网程序/bot 关联的用户代理|
|913110|找到了与安全扫描程序关联的请求标头|
|913120|找到了与安全扫描程序关联的请求文件名/参数|


### <a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|说明|
|---|---|
|920100|无效的 HTTP 请求行|
|920120|尝试的多部分/窗体数据绕过|
|920121|尝试的多部分/窗体数据绕过|
|920130|未能分析请求正文。|
|920140|多部分请求正文未通过严格验证|
|920160|Content-Length HTTP 标头不是数字。|
|920170|包含正文内容的 GET 或 HEAD 请求。|
|920171|带有传输编码的 GET 或 HEAD 请求。|
|920180|POST 请求缺少 Content-Length 标头。|
|920190|范围 = 最后一个字节值无效。|
|920200|范围 = 字段太多（6 个或以上）|
|920201|范围 = pdf 请求的字段在多（35 个或以上）|
|920202|范围 = pdf 请求的字段在多（6 个或以上）|
|920210|找到了多个/有冲突的连接标头数据。|
|920220|URL 编码滥用攻击企图|
|920230|检测到多个 URL 编码|
|920240|URL 编码滥用攻击企图|
|920250|UTF8 编码滥用攻击企图|
|920260|Unicode 全角/半角滥用攻击企图|
|920270|请求中的字符无效（null 字符）|
|920271|请求中的字符无效（不可列显的字符）|
|920272|请求中的字符无效（不属于 ascii 127 下面的可列显字符）|
|920273|请求中的字符无效（不属于极严格集）|
|920274|请求标头中的字符无效（不属于极严格集）|
|920280|请求缺少 Host 标头|
|920290|Host 标头为空|
|920300|请求缺少 Accept 标头|
|920310|请求包含空 Accept 标头|
|920311|请求包含空 Accept 标头|
|920320|缺少用户代理标头|
|920330|用户代理标头为空|
|920340|请求包含内容但缺少 Content-Type 标头|
|920341|包含内容的请求需要 Content-type 标头|
|920350|Host 标头是数字 IP 地址|
|920360|参数名称太长|
|920370|参数值太长|
|920380|请求中的参数太多|
|920390|超出了总参数大小|
|920400|上传的文件太大|
|920410|上传的文件总大小太大|
|920420|请求内容类型不受策略允许|
|920430|HTTP 协议版本不受策略允许|
|920440|策略限制了 URL 文件扩展名|
|920450|策略限制了 HTTP 标头 (%@{MATCHED_VAR})|
|920460|异常转义符|
|920470|非法的 Content-type 标头|
|920480|限制 content-type 标头中的字符集参数|

### <a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|说明|
|---|---|
|921110|HTTP 请求走私攻击|
|921120|HTTP 响应拆分攻击|
|921130|HTTP 响应拆分攻击|
|921140|通过标头展开的 HTTP 标头注入攻击|
|921150|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921151|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921160|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF 和标头名称）|
|921170|HTTP 参数污染|
|921180|HTTP 参数污染（% {TX. 1}）|

### <a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|说明|
|---|---|
|930100|路径遍历攻击 (/../)|
|930110|路径遍历攻击 (/../)|
|930120|OS 文件访问企图|
|930130|受限文件访问企图|

### <a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|说明|
|---|---|
|931100|可能的远程文件包含 (RFI) 攻击 = 使用 IP 地址的 URL 参数|
|931110|可能的远程文件包含 (RFI) 攻击 = 对 URL 有效负载使用常见 RFI 漏洞参数名使用|
|931120|可能的远程文件包含 (RFI) 攻击 = 在 URL 有效负载中使用尾随问号 (?)|
|931130|可能的远程文件包含 (RFI) 攻击 = 域外引用/链接|

### <a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|说明|
|---|---|
|932100|远程命令执行： Unix 命令注入|
|932105|远程命令执行： Unix 命令注入|
|932106|远程命令执行： Unix 命令注入|
|932110|远程命令执行： Windows 命令注入|
|932115|远程命令执行： Windows 命令注入|
|932120|远程命令执行 = 找到 Windows PowerShell 命令|
|932130|远程命令执行 = 找到 Unix Shell 表达式|
|932140|远程命令执行 = 找到 Windows FOR/IF 命令|
|932160|远程命令执行 = 找到 Unix Shell 代码|
|932170|远程命令执行 = Shellshock (CVE-2014-6271)|
|932171|远程命令执行 = Shellshock (CVE-2014-6271)|
|932180|受限文件上传尝试|
|932190|远程命令执行：通配符绕过方法尝试|

### <a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|说明|
|---|---|
|933100|PHP 注入攻击 = 找到开始/结束标记|
|933110|PHP 注入攻击 = 找到 PHP 脚本文件上传|
|933111|PHP 注入攻击：找到 PHP 脚本文件上传|
|933120|PHP 注入攻击 = 找到配置指令|
|933130|PHP 注入攻击 = 找到变量|
|933131|PHP 注入攻击：找到变量|
|933140|PHP 注入攻击：发现 i/o 流|
|933150|PHP 注入攻击 = 找到高风险的 PHP 函数名称|
|933151|PHP 注入攻击：找到了中等风险的 PHP 函数名称|
|933160|PHP 注入攻击 = 找到高风险的 PHP 函数调用|
|933161|PHP 注入攻击：找到低值 PHP 函数调用|
|933170|PHP 注入攻击：序列化对象注入|
|933180|PHP 注入攻击 = 找到可变函数调用|
|933190|PHP 注入攻击：找到 PHP 结束标记|

### <a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|说明|
|---|---|
|941100|检测到通过 libinjection 展开的 XSS 攻击|
|941101|检测到通过 libinjection 展开的 XSS 攻击|
|941110|XSS 筛选器 - 类别 1 = 脚本标记向量|
|941130|XSS 筛选器 - 类别 3 = 属性向量|
|941140|XSS 筛选器 - 类别 4 = Javascript URI 向量|
|941150|XSS 筛选器 - 类别 5 = 不允许的 HTML 属性|
|941160|NoScript XSS InjectionChecker： HTML 注入|
|941170|NoScript XSS InjectionChecker：属性注入|
|941180|节点验证器方块列表关键字|
|941190|使用样式表的 XSS|
|941200|使用 VML 帧的 XSS|
|941210|使用模糊 Javascript 的 XSS|
|941220|使用模糊 VB 脚本的 XSS|
|941230|使用 "嵌入" 标记的 XSS|
|941240|使用 "import" 或 "实现" 特性的 XSS|
|941250|IE XSS 筛选器-检测到攻击|
|941260|使用 "meta" 标记的 XSS|
|941270|使用 "链接" href 的 XSS|
|941280|使用 "base" 标记的 XSS|
|941290|使用 "applet" 标记的 XSS|
|941300|使用 "object" 标记的 XSS|
|941310|US-ASCII 格式错误编码 XSS 筛选器 - 检测到攻击。|
|941320|检测到可能的 XSS 攻击 - HTML 标记处理程序|
|941330|IE XSS 筛选器 - 检测到攻击。|
|941340|IE XSS 筛选器 - 检测到攻击。|
|941350|UTF-7 编码 IE XSS - 检测到攻击。|


### <a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|说明|
|---|---|
|942100|检测到通过 libinjection 展开的 SQL 注入攻击|
|942110|SQL 注入式攻击：检测到常见注入测试|
|942130|SQL 注入攻击：检测到 SQL Tautology。|
|942140|SQL 注入攻击 = 检测到常用 DB 名称|
|942150|SQL 注入攻击|
|942160|检测到使用 sleep() 或 benchmark() 的盲注 sqli 测试。|
|942170|检测到包含条件查询的 SQL 基准和休眠注入企图|
|942180|检测到基本的 SQL 身份验证绕过尝试1/3|
|942190|检测到 MSSQL 代码执行和信息收集尝试|
|942200|检测到 MySQL 注释/空间经过模糊处理的注入和反引号终止|
|942210|检测链接的 SQL 注入尝试1/2|
|942220|查找整数溢出攻击，这些攻击来自 skipfish （3.0.00738585072 除外）|
|942230|检测到条件 SQL 注入企图|
|942240|检测 MySQL 字符集开关和 MSSQL DoS 尝试|
|942250|检测匹配、合并和执行立即注入|
|942251|检测 HAVING 注入|
|942260|检测到基本 SQL 身份验证绕过尝试 2/3|
|942270|正在查找基本 sql 注入。 Mysql oracle 和其他人的常见攻击字符串|
|942280|检测 Postgres pg_sleep 注入、waitfor 延迟攻击和数据库关闭尝试|
|942290|查找基本 MongoDB SQL 注入企图|
|942300|检测到 MySQL 注释、条件和 ch(a)r 注入|
|942310|检测链接的 SQL 注入尝试2/2|
|942320|检测 MySQL 和 PostgreSQL 存储过程/函数注入|
|942330|检测到经典 SQL 注入探测 1/2|
|942340|检测到基本 SQL 身份验证绕过尝试 3/3|
|942350|检测 MySQL UDF 注入和其他数据/结构操作企图|
|942360|检测到连接的基本 SQL 注入和 SQLLFI 尝试|
|942361|基于关键字 alter 或 union 检测基本 SQL 注入|
|942370|检测到经典 SQL 注入探测 2/2|
|942380|SQL 注入攻击|
|942390|SQL 注入攻击|
|942400|SQL 注入攻击|
|942410|SQL 注入攻击|
|942420|受限制的 SQL 字符异常检测（cookie）：超出了特殊字符数（8）|
|942421|受限制的 SQL 字符异常检测（cookie）：超出了特殊字符数（3）|
|942430|受限 SQL 字符异常情况检测 (args)：已超出特殊字符数 (12)|
|942431|受限制的 SQL 字符异常检测（args）：超出了特殊字符数（6）|
|942432|受限制的 SQL 字符异常检测（args）：超出了特殊字符数（2）|
|942440|检测到 SQL 注释序列。|
|942450|识别到 SQL 十六进制编码|
|942460|元字符异常检测警报 - 重复的非单词字符|
|942470|SQL 注入攻击|
|942480|SQL 注入攻击|
|942490|检测经典 SQL 注入 probings 3/3|

### <a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|说明|
|---|---|
|943100|可能的会话固定攻击 = 在 HTML 中设置 Cookie 值|
|943110|可能的会话固定攻击 = 包含域外引用方的 SessionID 参数名称|
|943120|可能的会话固定攻击 = 不包含引用方的 SessionID 参数名称|

### <a name="crs944-31"></a> <p x-ms-format-detection="none">请求-944-应用程序-会话-JAVA</p>

|RuleId|说明|
|---|---|
|944120|可能的负载执行和远程命令执行|
|944130|可疑 Java 类|
|944200|利用 Java 反序列化 Apache Commons|

# <a name="owasp-30"></a>[OWASP 3。0](#tab/owasp30)

## <a name="owasp30"></a>规则集

### <a name="general-30"></a> <p x-ms-format-detection="none">常规</p>

|RuleId|说明|
|---|---|
|200004|可能的多部分不匹配边界。|

### <a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|说明|
|---|---|
|911100|方法不受策略允许|


### <a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|说明|
|---|---|
|913100|找到了与安全扫描程序关联的用户代理|
|913110|找到了与安全扫描程序关联的请求标头|
|913120|找到了与安全扫描程序关联的请求文件名/参数|
|913101|找到了与脚本/通用 HTTP 客户端关联的用户代理|
|913102|找到了与 Web 爬网程序/bot 关联的用户代理|

### <a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|说明|
|---|---|
|920100|无效的 HTTP 请求行|
|920130|未能分析请求正文。|
|920140|多部分请求正文未通过严格验证|
|920160|Content-Length HTTP 标头不是数字。|
|920170|包含正文内容的 GET 或 HEAD 请求。|
|920180|POST 请求缺少 Content-Length 标头。|
|920190|范围 = 最后一个字节值无效。|
|920210|找到了多个/有冲突的连接标头数据。|
|920220|URL 编码滥用攻击企图|
|920240|URL 编码滥用攻击企图|
|920250|UTF8 编码滥用攻击企图|
|920260|Unicode 全角/半角滥用攻击企图|
|920270|请求中的字符无效（null 字符）|
|920280|请求缺少 Host 标头|
|920290|Host 标头为空|
|920310|请求包含空 Accept 标头|
|920311|请求包含空 Accept 标头|
|920330|用户代理标头为空|
|920340|请求包含内容但缺少 Content-Type 标头|
|920350|Host 标头是数字 IP 地址|
|920380|请求中的参数太多|
|920360|参数名称太长|
|920370|参数值太长|
|920390|超出了总参数大小|
|920400|上传的文件太大|
|920410|上传的文件总大小太大|
|920420|请求内容类型不受策略允许|
|920430|HTTP 协议版本不受策略允许|
|920440|策略限制了 URL 文件扩展名|
|920450|策略限制了 HTTP 标头 (%@{MATCHED_VAR})|
|920200|范围 = 字段太多（6 个或以上）|
|920201|范围 = pdf 请求的字段在多（35 个或以上）|
|920230|检测到多个 URL 编码|
|920300|请求缺少 Accept 标头|
|920271|请求中的字符无效（不可列显的字符）|
|920320|缺少用户代理标头|
|920272|请求中的字符无效（不属于 ascii 127 下面的可列显字符）|
|920202|范围 = pdf 请求的字段在多（6 个或以上）|
|920273|请求中的字符无效（不属于极严格集）|
|920274|请求标头中的字符无效（不属于极严格集）|
|920460|异常转义符|

### <a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|说明|
|---|---|
|921100|HTTP 请求走私攻击。|
|921110|HTTP 请求走私攻击|
|921120|HTTP 响应拆分攻击|
|921130|HTTP 响应拆分攻击|
|921140|通过标头展开的 HTTP 标头注入攻击|
|921150|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921160|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF 和标头名称）|
|921151|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921170|HTTP 参数污染|
|921180|HTTP 参数污染 (%@{TX.1})|

### <a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|说明|
|---|---|
|930100|路径遍历攻击 (/../)|
|930110|路径遍历攻击 (/../)|
|930120|OS 文件访问企图|
|930130|受限文件访问企图|

### <a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|说明|
|---|---|
|931100|可能的远程文件包含 (RFI) 攻击 = 使用 IP 地址的 URL 参数|
|931110|可能的远程文件包含 (RFI) 攻击 = 对 URL 有效负载使用常见 RFI 漏洞参数名使用|
|931120|可能的远程文件包含 (RFI) 攻击 = 在 URL 有效负载中使用尾随问号 (?)|
|931130|可能的远程文件包含 (RFI) 攻击 = 域外引用/链接|

### <a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|说明|
|---|---|
|932120|远程命令执行 = 找到 Windows PowerShell 命令|
|932130|远程命令执行 = 找到 Unix Shell 表达式|
|932140|远程命令执行 = 找到 Windows FOR/IF 命令|
|932160|远程命令执行 = 找到 Unix Shell 代码|
|932170|远程命令执行 = Shellshock (CVE-2014-6271)|
|932171|远程命令执行 = Shellshock (CVE-2014-6271)|

### <a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|说明|
|---|---|
|933100|PHP 注入攻击 = 找到开始/结束标记|
|933110|PHP 注入攻击 = 找到 PHP 脚本文件上传|
|933120|PHP 注入攻击 = 找到配置指令|
|933130|PHP 注入攻击 = 找到变量|
|933150|PHP 注入攻击 = 找到高风险的 PHP 函数名称|
|933160|PHP 注入攻击 = 找到高风险的 PHP 函数调用|
|933180|PHP 注入攻击 = 找到可变函数调用|
|933151|PHP 注入攻击 = 找到中等风险的 PHP 函数名称|
|933131|PHP 注入攻击 = 找到变量|
|933161|PHP 注入攻击 = 找到低值 PHP 函数调用|
|933111|PHP 注入攻击 = 找到 PHP 脚本文件上传|

### <a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|说明|
|---|---|
|941100|检测到通过 libinjection 展开的 XSS 攻击|
|941110|XSS 筛选器 - 类别 1 = 脚本标记向量|
|941130|XSS 筛选器 - 类别 3 = 属性向量|
|941140|XSS 筛选器 - 类别 4 = Javascript URI 向量|
|941150|XSS 筛选器 - 类别 5 = 不允许的 HTML 属性|
|941180|节点验证器方块列表关键字|
|941190|使用样式表的 XSS|
|941200|使用 VML 帧的 XSS|
|941210|使用模糊 Javascript 的 XSS|
|941220|使用模糊 VB 脚本的 XSS|
|941230|使用 "嵌入" 标记的 XSS|
|941240|使用 "import" 或 "实现" 特性的 XSS|
|941260|使用 "meta" 标记的 XSS|
|941270|使用 "链接" href 的 XSS|
|941280|使用 "base" 标记的 XSS|
|941290|使用 "applet" 标记的 XSS|
|941300|使用 "object" 标记的 XSS|
|941310|US-ASCII 格式错误编码 XSS 筛选器 - 检测到攻击。|
|941330|IE XSS 筛选器 - 检测到攻击。|
|941340|IE XSS 筛选器 - 检测到攻击。|
|941350|UTF-7 编码 IE XSS - 检测到攻击。|
|941320|检测到可能的 XSS 攻击 - HTML 标记处理程序|

### <a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|说明|
|---|---|
|942100|检测到通过 libinjection 展开的 SQL 注入攻击|
|942110|SQL 注入式攻击：检测到常见注入测试|
|942130|SQL 注入攻击：检测到 SQL Tautology。|
|942140|SQL 注入攻击 = 检测到常用 DB 名称|
|942160|检测到使用 sleep() 或 benchmark() 的盲注 sqli 测试。|
|942170|检测到包含条件查询的 SQL 基准和休眠注入企图|
|942190|检测到 MSSQL 代码执行和信息收集尝试|
|942200|检测到 MySQL 注释/空间经过模糊处理的注入和反引号终止|
|942230|检测到条件 SQL 注入企图|
|942260|检测到基本 SQL 身份验证绕过尝试 2/3|
|942270|正在查找基本 sql 注入。 针对 mysql oracle 和其他系统的常见攻击字符串。|
|942290|查找基本 MongoDB SQL 注入企图|
|942300|检测到 MySQL 注释、条件和 ch(a)r 注入|
|942310|检测链接的 SQL 注入尝试2/2|
|942320|检测 MySQL 和 PostgreSQL 存储过程/函数注入|
|942330|检测到经典 SQL 注入探测 1/2|
|942340|检测到基本 SQL 身份验证绕过尝试 3/3|
|942350|检测 MySQL UDF 注入和其他数据/结构操作企图|
|942360|检测到连接的基本 SQL 注入和 SQLLFI 尝试|
|942370|检测到经典 SQL 注入探测 2/2|
|942150|SQL 注入攻击|
|942410|SQL 注入攻击|
|942430|受限 SQL 字符异常情况检测 (args)：已超出特殊字符数 (12)|
|942440|检测到 SQL 注释序列。|
|942450|识别到 SQL 十六进制编码|
|942251|检测 HAVING 注入|
|942460|元字符异常检测警报 - 重复的非单词字符|

### <a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|说明|
|---|---|
|943100|可能的会话固定攻击 = 在 HTML 中设置 Cookie 值|
|943110|可能的会话固定攻击 = 包含域外引用方的 SessionID 参数名称|
|943120|可能的会话固定攻击 = 不包含引用方的 SessionID 参数名称|

# <a name="owasp-229"></a>[OWASP 2.2。9](#tab/owasp2)

## <a name="owasp229"></a>规则集

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|说明|
|---|---|
|960911|无效的 HTTP 请求行|
|981227|Apache 错误 = 请求中的 URI 无效。|
|960912|未能分析请求正文。|
|960914|多部分请求正文未通过严格验证|
|960915|多部分分析器检测到可能的不匹配边界。|
|960016|Content-Length HTTP 标头不是数字。|
|960011|包含正文内容的 GET 或 HEAD 请求。|
|960012|POST 请求缺少 Content-Length 标头。|
|960902|标识编码用法无效。|
|960022|HTTP 1.0 不允许 Expect 标头。|
|960020|Pragma 标头需要 HTTP/1.1 请求的 Cache-Control 标头。|
|958291|范围 = 字段存在并以 0 开始。|
|958230|范围 = 最后一个字节值无效。|
|958295|找到了多个/有冲突的连接标头数据。|
|950107|URL 编码滥用攻击企图|
|950109|检测到多个 URL 编码|
|950108|URL 编码滥用攻击企图|
|950801|UTF8 编码滥用攻击企图|
|950116|Unicode 全角/半角滥用攻击企图|
|960901|请求中的字符无效|
|960018|请求中的字符无效|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|说明|
|---|---|
|960008|请求缺少 Host 标头|
|960007|Host 标头为空|
|960015|请求缺少 Accept 标头|
|960021|请求包含空 Accept 标头|
|960009|请求缺少用户代理标头|
|960006|用户代理标头为空|
|960904|请求包含内容但缺少 Content-Type 标头|
|960017|Host 标头是数字 IP 地址|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|说明|
|---|---|
|960209|参数名称太长|
|960208|参数值太长|
|960335|请求中的参数太多|
|960341|超出了总参数大小|
|960342|上传的文件太大|
|960343|上传的文件总大小太大|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|说明|
|---|---|
|960032|方法不受策略允许|
|960010|请求内容类型不受策略允许|
|960034|HTTP 协议版本不受策略允许|
|960035|策略限制了 URL 文件扩展名|
|960038|策略限制了 HTTP 标头|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|说明|
|---|---|
|990002|请求指示安全扫描程序已扫描站点|
|990901|请求指示安全扫描程序已扫描站点|
|990902|请求指示安全扫描程序已扫描站点|
|990012|恶意网站爬网程序|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|说明|
|---|---|
|960024|元字符异常检测警报 - 重复的非单词字符|
|950008|注入未记录的 ColdFusion 标记|
|950010|LDAP 注入攻击|
|950011|SSI 注入攻击|
|950018|检测到通用 PDF XSS URL。|
|950019|电子邮件注入攻击|
|950012|HTTP 请求走私攻击。|
|950910|HTTP 响应拆分攻击|
|950911|HTTP 响应拆分攻击|
|950117|远程文件包含攻击|
|950118|远程文件包含攻击|
|950119|远程文件包含攻击|
|950120|可能的远程文件包含 (RFI) 攻击 = 域外引用/链接|
|981133|规则 981133|
|981134|规则 981134|
|950009|会话固定攻击|
|950003|会话固定|
|950000|会话固定|
|950005|远程文件访问企图|
|950002|系统命令访问|
|950006|系统命令注入|
|959151|PHP 注入攻击|
|958976|PHP 注入攻击|
|958977|PHP 注入攻击|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|说明|
|---|---|
|981231|检测到 SQL 注释序列。|
|981260|识别到 SQL 十六进制编码|
|981320|SQL 注入攻击 = 检测到常用 DB 名称|
|981300|规则 981300|
|981301|规则 981301|
|981302|规则 981302|
|981303|规则 981303|
|981304|规则 981304|
|981305|规则 981305|
|981306|规则 981306|
|981307|规则 981307|
|981308|规则 981308|
|981309|规则 981309|
|981310|规则 981310|
|981311|规则 981311|
|981312|规则 981312|
|981313|规则 981313|
|981314|规则 981314|
|981315|规则 981315|
|981316|规则 981316|
|981317|SQL SELECT 语句异常检测警报|
|950007|SQL 盲注攻击|
|950001|SQL 注入攻击|
|950908|SQL 注入攻击。|
|959073|SQL 注入攻击|
|981272|检测到使用 sleep() 或 benchmark() 的盲注 sqli 测试。|
|981250|检测到包含条件查询的 SQL 基准和休眠注入企图|
|981241|检测到条件 SQL 注入企图|
|981276|正在查找基本 sql 注入。 针对 mysql oracle 和其他系统的常见攻击字符串。|
|981270|查找基本 MongoDB SQL 注入企图|
|981253|检测 MySQL 和 PostgreSQL 存储过程/函数注入|
|981251|检测 MySQL UDF 注入和其他数据/结构操作企图|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|说明|
|---|---|
|973336|XSS 筛选器 - 类别 1 = 脚本标记向量|
|973338|XSS 筛选器 - 类别 3 = Javascript URI 向量|
|981136|规则 981136|
|981018|规则 981018|
|958016|跨站点脚本 (XSS) 攻击|
|958414|跨站点脚本 (XSS) 攻击|
|958032|跨站点脚本 (XSS) 攻击|
|958026|跨站点脚本 (XSS) 攻击|
|958027|跨站点脚本 (XSS) 攻击|
|958054|跨站点脚本 (XSS) 攻击|
|958418|跨站点脚本 (XSS) 攻击|
|958034|跨站点脚本 (XSS) 攻击|
|958019|跨站点脚本 (XSS) 攻击|
|958013|跨站点脚本 (XSS) 攻击|
|958408|跨站点脚本 (XSS) 攻击|
|958012|跨站点脚本 (XSS) 攻击|
|958423|跨站点脚本 (XSS) 攻击|
|958002|跨站点脚本 (XSS) 攻击|
|958017|跨站点脚本 (XSS) 攻击|
|958007|跨站点脚本 (XSS) 攻击|
|958047|跨站点脚本 (XSS) 攻击|
|958410|跨站点脚本 (XSS) 攻击|
|958415|跨站点脚本 (XSS) 攻击|
|958022|跨站点脚本 (XSS) 攻击|
|958405|跨站点脚本 (XSS) 攻击|
|958419|跨站点脚本 (XSS) 攻击|
|958028|跨站点脚本 (XSS) 攻击|
|958057|跨站点脚本 (XSS) 攻击|
|958031|跨站点脚本 (XSS) 攻击|
|958006|跨站点脚本 (XSS) 攻击|
|958033|跨站点脚本 (XSS) 攻击|
|958038|跨站点脚本 (XSS) 攻击|
|958409|跨站点脚本 (XSS) 攻击|
|958001|跨站点脚本 (XSS) 攻击|
|958005|跨站点脚本 (XSS) 攻击|
|958404|跨站点脚本 (XSS) 攻击|
|958023|跨站点脚本 (XSS) 攻击|
|958010|跨站点脚本 (XSS) 攻击|
|958411|跨站点脚本 (XSS) 攻击|
|958422|跨站点脚本 (XSS) 攻击|
|958036|跨站点脚本 (XSS) 攻击|
|958000|跨站点脚本 (XSS) 攻击|
|958018|跨站点脚本 (XSS) 攻击|
|958406|跨站点脚本 (XSS) 攻击|
|958040|跨站点脚本 (XSS) 攻击|
|958052|跨站点脚本 (XSS) 攻击|
|958037|跨站点脚本 (XSS) 攻击|
|958049|跨站点脚本 (XSS) 攻击|
|958030|跨站点脚本 (XSS) 攻击|
|958041|跨站点脚本 (XSS) 攻击|
|958416|跨站点脚本 (XSS) 攻击|
|958024|跨站点脚本 (XSS) 攻击|
|958059|跨站点脚本 (XSS) 攻击|
|958417|跨站点脚本 (XSS) 攻击|
|958020|跨站点脚本 (XSS) 攻击|
|958045|跨站点脚本 (XSS) 攻击|
|958004|跨站点脚本 (XSS) 攻击|
|958421|跨站点脚本 (XSS) 攻击|
|958009|跨站点脚本 (XSS) 攻击|
|958025|跨站点脚本 (XSS) 攻击|
|958413|跨站点脚本 (XSS) 攻击|
|958051|跨站点脚本 (XSS) 攻击|
|958420|跨站点脚本 (XSS) 攻击|
|958407|跨站点脚本 (XSS) 攻击|
|958056|跨站点脚本 (XSS) 攻击|
|958011|跨站点脚本 (XSS) 攻击|
|958412|跨站点脚本 (XSS) 攻击|
|958008|跨站点脚本 (XSS) 攻击|
|958046|跨站点脚本 (XSS) 攻击|
|958039|跨站点脚本 (XSS) 攻击|
|958003|跨站点脚本 (XSS) 攻击|
|973300|检测到可能的 XSS 攻击 - HTML 标记处理程序|
|973301|检测到 XSS 攻击|
|973302|检测到 XSS 攻击|
|973303|检测到 XSS 攻击|
|973304|检测到 XSS 攻击|
|973305|检测到 XSS 攻击|
|973306|检测到 XSS 攻击|
|973307|检测到 XSS 攻击|
|973308|检测到 XSS 攻击|
|973309|检测到 XSS 攻击|
|973311|检测到 XSS 攻击|
|973313|检测到 XSS 攻击|
|973314|检测到 XSS 攻击|
|973331|IE XSS 筛选器 - 检测到攻击。|
|973315|IE XSS 筛选器 - 检测到攻击。|
|973330|IE XSS 筛选器 - 检测到攻击。|
|973327|IE XSS 筛选器 - 检测到攻击。|
|973326|IE XSS 筛选器 - 检测到攻击。|
|973346|IE XSS 筛选器 - 检测到攻击。|
|973345|IE XSS 筛选器 - 检测到攻击。|
|973324|IE XSS 筛选器 - 检测到攻击。|
|973323|IE XSS 筛选器 - 检测到攻击。|
|973348|IE XSS 筛选器 - 检测到攻击。|
|973321|IE XSS 筛选器 - 检测到攻击。|
|973320|IE XSS 筛选器 - 检测到攻击。|
|973318|IE XSS 筛选器 - 检测到攻击。|
|973317|IE XSS 筛选器 - 检测到攻击。|
|973329|IE XSS 筛选器 - 检测到攻击。|
|973328|IE XSS 筛选器 - 检测到攻击。|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|说明|
|---|---|
|950103|路径遍历攻击|

### <a name="crs45"></a> crs_45_trojans

|RuleId|说明|
|---|---|
|950110|后门访问|
|950921|后门访问|
|950922|后门访问|

---

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-portal.md)
