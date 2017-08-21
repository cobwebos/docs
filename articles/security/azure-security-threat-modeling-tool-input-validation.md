---
title: "输入验证 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "针对威胁建模工具中暴露的威胁采取的缓解措施"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: rodsan
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 76584799ad98edc9af1580af4f5bd1bbd20a7e85
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---

# <a name="security-frame-input-validation--mitigations"></a>安全框架：输入验证 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| Web 应用程序 | <ul><li>[使用不受信任样式表针对所有转换禁用 XSLT 脚本](#disable-xslt)</li><li>[确保可能包含用户可控内容的每个页面能够选择不使用自动 MIME 探查](#out-sniffing)</li><li>[强化或禁用 XML 实体解析](#xml-resolution)</li><li>[使用 http.sys 的应用程序执行 URL 规范化验证](#app-verification)</li><li>[确保接受用户的文件时实施适当的控制](#controls-users)</li><li>[确保在 Web 应用程序中使用类型安全的参数进行数据访问](#typesafe)</li><li>[使用单独的模型绑定类或绑定筛选列表来防止 MVC 大规模分配漏洞](#binding-mvc)</li><li>[在呈现之前为不受信任的 Web 输出编码](#rendering)</li><li>[针对所有字符串类型的 Model 属性执行输入验证和筛选](#typemodel)</li><li>[应该针对接受所有字符的窗体字段（例如 RTF 编辑器）应用净化](#richtext)</li><li>[不要将 DOM 元素分配到没有内置编码的接收器](#inbuilt-encode)</li><li>[验证应用程序中的所有重定向是否闭合且安全](#redirect-safe)</li><li>[针对 Controller 方法接受的所有字符串类型参数实施输入验证](#string-method)</li><li>[针对正则表达式处理设置超时上限，防止由于正则表达式错误而出现 DoS](#dos-expression)</li><li>[避免在 Razor 视图中使用 Html.Raw](#html-razor)</li></ul> | 
| 数据库 | <ul><li>[不要在存储过程中使用动态查询](#stored-proc)</li></ul> | 
| Web API | <ul><li>[确保针对 Web API 方法执行模型验证](#validation-api)</li><li>[针对 Web API 方法接受的所有字符串类型参数实施输入验证](#string-api)</li><li>[确保在 Web API 中使用类型安全的参数进行数据访问](#typesafe-api)</li></ul> | 
| Azure Document DB | <ul><li>[对 Azure Cosmos DB 使用参数化 SQL 查询](#sql-docdb)</li></ul> | 
| WCF | <ul><li>[通过架构绑定执行 WCF 输入验证](#schema-binding)</li><li>[WCF - 通过参数检查器执行输入验证](#parameters)</li></ul> | 

## <a id="disable-xslt"></a>使用不受信任样式表针对所有转换禁用 XSLT 脚本

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [XSLT 安全](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx)、[XsltSettings.EnableScript 属性](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| 步骤 | XSLT 支持使用 `<msxml:script>` 元素在样式表内编写脚本。 这样，便可以在 XSLT 转换中使用自定义函数。 该脚本在执行转换的进程的上下文中执行。 在不受信任的环境中必须禁用 XSLT 脚本，防止执行不受信任的代码。 *如果使用.NET：*XSLT 脚本默认已禁用；但是，必须确保未通过 `XsltSettings.EnableScript` 属性显式将它启用。|

### <a name="example"></a>示例 

```C#
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>示例
如果使用.MSXML 6.0，XSLT 脚本默认已禁用；但是，必须确保未通过 XML DOM 对象属性 AllowXsltScript 显式将它启用。 

```C#
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>示例
如果使用 MSXML 5 或更低版本，XSLT 脚本默认已启用，必须显式将它禁用。 将 XML DOM 对象属性 AllowXsltScript 设置为 false。 

```C#
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>确保可能包含用户可控内容的每个页面能够选择不使用自动 MIME 探查

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [IE8 安全性第五部分 - 全面保护](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| 步骤 | <p>对于可能包含用户可控内容的每个页面，必须使用 HTTP 标头 `X-Content-Type-Options:nosniff`。 为了符合此要求，可以仅针对可能包含用户可控内容的页面逐页设置所需的标头，或者针对应用程序中的所有页面全局设置该标头。</p><p>从 Web 服务器传送的每种类型的文件都有一个关联的 [MIME 类型](http://en.wikipedia.org/wiki/Mime_type)（也称为 *content-type*），该类型描述内容的性质（即，图像、文本、应用程序，等等）</p><p>X-Content-Type-Options 标头是一个 HTTP 标头，可让开发人员指定不应该对其内容使用 MIME 探查。 此标头旨在缓解 MIME 探查攻击。 Internet Explorer 8 (IE8) 中已添加对此标头的支持</p><p>只有 Internet Explorer 8 (IE8) 的用户才能受益于 X-Content-Type-Options。 以前的 Internet Explorer 版本目前不支持 X-Content-Type-Options 标头</p><p>Internet Explorer 8（和更高版本）是能够实现 MIME 探查选择退出功能的唯一主流浏览器。 如果其他主流浏览器（Firefox、Safari、Chrome）可实现类似功能，此项建议将会更新，以包括这些浏览器的语法</p>|

### <a name="example"></a>示例
若要针对应用程序中的所有页面全局启用所需的标头，可执行以下操作之一： 

* 如果应用程序由 Internet Information Services (IIS) 7 托管，请在 web.config 文件中添加该标头。 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* 通过全局 Application\_BeginRequest 添加该标头 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* 实现自定义的 HTTP 模块 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* 对于特定的页面，只能通过将所需的标头添加到单个响应来启用该标头： 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>强化或禁用 XML 实体解析

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [XML 实体扩展](http://capec.mitre.org/data/definitions/197.html)、[XML 拒绝服务攻击和防护](http://msdn.microsoft.com/magazine/ee335713.aspx)、[MSXML 安全概述](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx)、[有关保护 MSXML 代码的最佳做法](http://msdn.microsoft.com/library/ms759188(VS.85).aspx)、[NSXMLParserDelegate 协议参考](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html)、[解析外部引用](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| 步骤| <p>在 XML 中，有一项功能尽管未得到广泛使用，但可让 XML 分析器使用文档本身内部定义的或者外部源中定义的值来扩展宏实体。 例如，文档中可能定义了值为“Microsoft”的实体“companyname”，因此，每当文档中出现文本“&companyname;”时，该文本就自动被文本“Microsoft”替换。 或者，文档中可能定义了实体“MSFTStock”，用于引用外部 Web 服务来提取当前的 Microsoft 股价。</p><p>因此，每当文档中出现“&MSFTStock;”时，该值就会自动被当前股价替换。 但是，如果此功能使用不当，则可能会造成拒绝服务 (DoS) 攻击。 攻击者可以嵌套多个实体来制造指数扩张式 XML 炸弹，耗尽系统中所有可用的内存。 </p><p>或者，攻击者可以创建一个外部引用，流式传输无限数量的数据，或者只是将线程挂起。 因此，如果应用程序不使用内部和/或外部 XML 实体解析，则必须完全禁用该功能；或者，如果绝对有必要使用此功能，需手动限制应用程序执行实体解析时可以消耗的内存和时间。 如果应用程序不需要实体解析，请禁用该功能。 </p>|

### <a name="example"></a>示例
对于 .NET Framework 代码，可使用以下方法：

```C#
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
请注意，`ProhibitDtd` 在 `XmlReaderSettings` 中的默认值为 true，但在 `XmlTextReader` 中为 false。 如果使用 XmlReaderSettings，则不需要显式将 ProhibitDtd 设置为 true，但为安全起见，建议这样设置。 另请注意，XmlDocument 类默认允许实体解析。 

### <a name="example"></a>示例
若要对 XmlDocuments 禁用实体解析，请使用 Load 方法的 `XmlDocument.Load(XmlReader)` 重载，并在 XmlReader 参数中设置相应的属性来禁用解析，如以下代码中所示： 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>示例
如果无法对应用程序禁用实体解析，请根据应用程序的需要，将 XmlReaderSettings.MaxCharactersFromEntities 属性设置为某个合理值。 这样可以限制潜在的指数扩张 DoS 攻击的影响。 以下示例代码演示了此方法： 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>示例
如果需要解析内联实体但不需要解析外部实体，请将 XmlReaderSettings.XmlResolver 属性设置为 null。 例如： 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
请注意，在 MSXML6 中，ProhibitDTD 默认设置为 true（禁用 DTD 处理）。 对于 Apple OSX/iOS 代码，可以使用两个 XML 分析器：NSXMLParser 和 libXML2。 

## <a id="app-verification"></a>使用 http.sys 的应用程序执行 URL 规范化验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | <p>使用 http.sys 的任何应用程序应遵循以下指导原则：</p><ul><li>将 URL 的长度限制为不超过 16,384 个字符（ASCII 或 Unicode）。 这是基于默认 Internet Information Services (IIS) 6 设置的绝对最大 URL 长度。 在网站中，应该尽可能使用短于此长度的 URL。</li><li>使用标准的 .NET Framework 文件 I/O 类（例如 FileStream），因为这些类可以利用 .NET FX 中的规范化规则</li><li>显式构建已知文件名的允许列表</li><li>显式拒绝你不会针对其提供 UrlScan reject 的已知文件类型：exe、bat、cmd、com、htw、ida、idq、htr、idc、shtm[l]、stm、printer、ini、pol、dat 文件</li><li>捕获以下异常：<ul><li>System.ArgumentException（针对设备名称）</li><li>System.NotSupportedException（针对数据流）</li><li>System.IO.FileNotFoundException（针对无效的转义文件名）</li><li>System.IO.DirectoryNotFoundException（针对无效的转义目录）</li></ul></li><li>*不要*调用 Win32 文件 I/O API。 在 URL 无效时，应向用户正常返回 400 错误并记录实际错误。</li></ul>|

## <a id="controls-users"></a>确保接受用户的文件时实施适当的控制

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [不受限制的文件上传](https://www.owasp.org/index.php/Unrestricted_File_Upload)、[文件签名表](http://www.garykessler.net/library/file_sigs.html) |
| 步骤 | <p>上传的文件会给应用程序带来重大的风险。</p><p>许多攻击的第一步就是在要攻击的系统中放入一些代码。 然后，攻击者只需找到一种执行该代码的方法。 使用文件上传可以帮助攻击者实现这第一个步骤。 不受限制的文件上传的后果多种多样，包括完全接管系统、使文件系统或数据库过载、将攻击转移到后端系统，以及简单的篡改。</p><p>这种后果取决于应用程序对上传的文件的处理，尤其是该文件的存储位置。 服务器端缺少对文件上传的验证。 应该针对文件上传功能实施以下安全控制：</p><ul><li>文件扩展名检查（应该只接受有效的一组受允许文件类型）</li><li>文件大小上限</li><li>不应将文件上传到 webroot；位置应是非系统驱动器上的某个目录</li><li>应该遵守命名约定，使上传文件的名称具有某种随机性，防止文件覆盖</li><li>将文件写入磁盘之前，应扫描它是否包含病毒</li><li>确保验证文件名和其他任何元数据（例如文件路径）是否包含恶意字符</li><li>应检查文件格式签名，防止用户上传伪装的文件（例如，通过将扩展名更改为 txt 来上传 exe 文件）</li></ul>| 

### <a name="example"></a>示例
有关文件格式签名验证的最后一个要点，请参阅以下类的详细信息： 

```C#
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>确保在 Web 应用程序中使用类型安全的参数进行数据访问

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | <p>如果使用 Parameters 集合，SQL 会将输入视为文本值而不是可执行代码。 使用 Parameters 集合可针对输入数据实施类型和长度约束。 如果值超出范围，将触发异常。 如果不使用类型安全的 SQL 参数，攻击者可能会执行嵌入在未筛选输入中的注入式攻击。</p><p>构造 SQL 查询时请使用类型安全的参数，避免未筛选的输入发生 SQL 注入式攻击。 可在存储过程和动态 SQL 语句中使用类型安全的参数。 数据库将参数视为文本值而不是可执行代码。 此外，应检查参数的类型和长度。</p>|

### <a name="example"></a>示例 
以下代码演示在调用存储过程时，如何对 SqlParameterCollection 使用类型安全的参数。 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
在上面的代码示例中，输入值不能超过 11 个字符。 如果数据不符合该参数定义的类型或长度，SqlParameter 类将引发异常。 

## <a id="binding-mvc"></a>使用单独的模型绑定类或绑定筛选列表来防止 MVC 大规模分配漏洞

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | MVC5、MVC6 |
| 属性              | 不适用  |
| 参考              | [元数据特性](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute)、[公共密钥安全漏洞和缓解措施](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)、[有关 ASP.NET MVC 中的大规模分配的完整指南](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx)、[通过 MVC 开始使用 EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| 步骤 | <ul><li>**在什么情况下应该查找过度提交漏洞？**- 在绑定用户输入中的模型类的任何位置，都可能存在过度提交漏洞。 MVC 等框架可在自定义 .NET 类中表示用户数据，包括普通旧 CLR 对象 (POCO)。 MVC 会在这些模型类中自动填充请求中的数据，提供一种便利的表示形式来处理用户输入。 如果这些类包含不应由用户设置的属性，应用程序将很容易遭到过度提交攻击，使用户能够控制应用程序永远不希望他们控制的数据。 与 MVC 模型绑定一样，数据库访问技术（例如，包括 Entity Framework 在内的对象/关系映射器）通常也支持使用 POCO 对象来表示数据库数据。 使用这些数据模型类同样能够方便地处理数据库数据，就如同使用 MVC 处理用户输入一样。 由于 MVC 和数据库支持类似的模型（例如 POCO 对象），因此，似乎可以轻松地重复使用相同的类来实现这两种目的。 这种做法无法保留关注点分离，这是向模型绑定公共非预期属性，造成过度提交攻击的一个常见区域。</li><li>**为何不应使用未筛选的数据库模型类作为 MVC 操作的参数？**- 因为 MVC 模型绑定会绑定该类中的任何内容。 即使数据未显示在视图中，恶意用户也可以发送包含此数据的 HTTP 请求，而 MVC 会自然而然地绑定此请求，因为操作指出，数据库类就是它应该接受用户输入的数据形状。</li><li>**为何应关心用于模型绑定的形状？**- 结合过度扩散的模型使用 ASP.NET MVC 模型绑定会使应用程序曝露在过度提交攻击之下。 攻击者可能会利用过度提交来更改应用程序数据，使之超出开发人员的预期，例如，覆盖某个商品的价格，或者帐户的安全特权。 应用程序应使用操作特定的绑定模型（或特定的受允许属性筛选列表）来提供显式约定，规定允许通过模型绑定提供哪些不受信任的输入。</li><li>**是否存在只复制代码的单独绑定模型？**- 没有，这是一个关注点分离的问题。 如果在操作方法中重复使用数据库模型，则表示 HTTP 请求中的用户可以设置该类中的任何属性（或子属性）。 如果不希望 MVC 这样做，则需要使用筛选列表或单独的类形状来告知 MVC，可以通过用户输入提供哪些数据。</li><li>**如果针对用户输入使用单独的绑定模型，是否需要复制所有数据批注特性？**- 没有必要。 可在数据库模型类中使用 MetadataTypeAttribute 来链接到模型绑定类中的元数据。 只需注意，MetadataTypeAttribute 所引用的类型必须是引用类型（可能包含更少或更多的属性）的子集。</li><li>**在用户输入模型与数据库模型之间来回移动数据是个繁琐的过程。是否可以只使用反射来复制所有属性？**- 可以。 在绑定模型中显示的属性无非就是你确定对于用户输入安全的属性。 没有安全方面的理由会阻止使用反射来复制这两个模型之间共有的所有属性。</li><li>**对于 [Bind(Exclude ="â€¦")]，是否可以使用它来取代单独的绑定模型？**- 不建议采用这种做法。 使用 [Bind(Exclude ="â€¦")] 意味着任何新属性在默认情况下可绑定。 添加新属性后，记得执行一个额外的步骤来保持安全性，而不要采用默认的设计安全性。 根据具体的开发人员，每次添加一个属性都要检查此列表是有风险的。</li><li>**[Bind(Include ="â€¦")] 是否可用于 Edit 操作？**- 不可以。 [Bind(Include ="â€¦")] 仅适用于 INSERT 式操作（添加新数据）。 对于 UPDATE 式操作（修改现有数据），请使用另一种方式，例如，使用单独的绑定模型，或者将允许属性的显式列表传递给 UpdateModel 或 TryUpdateModel。 在 Edit 操作中添加 [Bind(Include ="â€¦")] 特性意味着 MVC 会创建一个对象实例并且仅设置列出的属性，将其他所有属性保留默认值。 如果数据是持久保存的，将完全替换现有实体，将所有已省略属性的值重置为默认值。 例如，如果在 Edit 操作的 [Bind(Include ="â€¦")] 特性中省略 IsAdmin，则名称已通过此操作进行编辑的任何用户将重置为 IsAdmin = false（任何编辑过的用户将丢失管理员状态）。 如果想要防止对某些属性进行更新，请使用上述其他方式之一。 请注意，某些版本的 MVC 工具会在 Edit 操作中生成包含 [Bind(Include ="â€¦")] 的控制器类，这意味着从该列表中删除某个属性可防止过度提交攻击。 但是，如上所述，这种方式无法按预期工作，而是将已省略属性中的任何数据重置为默认值。</li><li>**对于 Create 操作，使用 [Bind(Include ="â€¦")] 而不是单独的绑定模型是否需要注意什么？**- 是的。 首先，这种方式不适用于 Edit 方案，它需要保留采用两种不同的方式来缓解所有过度提交漏洞。 其次，单独的绑定模型会在用于用户输入的形状与用于持久性的形状之间实施关注点分离，而 [Bind(Include ="â€¦")] 则不会。 再次，请注意，[Bind(Include ="â€¦")] 只能处理顶级属性；不能只允许在特性中使用一部分子属性（例如“Details.Name”）。 最后，也许最重要的是，使用 [Bind(Include ="â€¦")] 会增加一个额外的步骤，每次将该类用于模型绑定时，都必须记得执行该步骤。 如果新的操作方法直接绑定到数据类而忘记包含 [Bind(Include ="â€¦")] 特性，则很容易遭到过度提交攻击，因此 [Bind(Include ="â€¦")] 方式在默认情况下不是那么安全。 如果使用 [Bind(Include ="â€¦")]，每次当数据类显示为操作方法参数时，请始终记得指定该特性。</li><li>**对于 Create 操作，如果将 [Bind(Include ="â€¦")] 特性放置在模型类本身中会怎样？使用这种方式是否就不需要记得在每个操作方法中放置该特性？**- 在某些情况下，这种方式是可行的。 在模型类型本身中（而不是在使用此类的操作参数中）使用 [Bind(Include ="â€¦")] 确实不需要记得在每个操作方法中包含 [Bind(Include ="â€¦")] 特性。 在类中直接使用该特性可以针对模型绑定目的，有效创建此类的独立外围应用。 但是，这种方式只允许为每个模型类创建一个模型绑定形状。 如果某个操作方法需要允许字段的模型绑定（例如，用于更新用户角色的仅限管理员的操作），而其他操作需要防止字段的模型绑定，则此方式不起作用。 每个类只能有一个模型绑定形状；如果不同的操作需要不同的模型绑定形状，它们需要在操作方法中使用单独的模型绑定类或单独的 [Bind(Include ="â€¦")] 特性来表示这些不同的形状。</li><li>**什么是绑定模型？它们与视图模型一样吗？**- 这是两个相关的概念。 术语“绑定模型”表示操作中使用的模型类是参数列表（从 MVC 模型绑定传递给操作方法的形状）。 术语“视图模型”表示从操作方法传递给视图的模型类。 我们经常使用视图特定的模型将数据从操作方法传递给视图。 通常，此形状也适用于模型绑定，术语“视图模型”可用于表示在这两个位置使用的同一模型。 为追求精确，本过程将专门讨论绑定模型并侧重于传递给操作的形状，这是有关大规模分配的一个要点。</li></ul>| 

## <a id="rendering"></a>在呈现之前为不受信任的 Web 输出编码

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、Web 窗体、MVC5、MVC6 |
| 属性              | 不适用  |
| 参考              | [如何在 ASP.NET 中防止跨站点脚本](http://msdn.microsoft.com/library/ms998274.aspx)、[跨站点脚本](http://cwe.mitre.org/data/definitions/79.html)、[XSS（跨站点脚本）预防速查表](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| 步骤 | 跨站点脚本（通常缩写为 XSS）是针对联机服务或者使用 Web 输入的任何应用程序/组件的攻击向量。 攻击者可能会利用 XSS 漏洞，通过有漏洞的 Web 应用程序在另一个用户的计算机上执行脚本。 使用恶意脚本可以窃取 Cookie，或者通过 JavaScript 篡改受害者的计算机。 通过验证用户输入，确保在网页中呈现之前其格式正确并经过编码，可以阻止 XSS。 可以使用 Web 保护库执行输入验证和输出编码。 对于托管代码（C\#、VB.net 等），可以根据显示用户输入的上下文，使用 Web 保护（反 XSS）库中一个或多个适当的编码方法：| 

### <a name="example"></a>示例

```C#
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>针对所有字符串类型的 Model 属性执行输入验证和筛选

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、MVC5、MVC6 |
| 属性              | 不适用  |
| 参考              | [添加验证](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation)、[验证 MVC 应用程序中的模型数据](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)、[ASP.NET MVC 应用程序指导原则](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| 步骤 | <p>在应用程序中使用所有输入参数之前必须验证这些参数，确保应用程序能够防御恶意用户输入的攻击。 结合允许列表验证策略，在服务器端使用正则表达式验证来验证输入值。 传递给方法的未经净化的用户输入/参数可能会导致代码注入安全漏洞。</p><p>对于 Web 应用程序而言，入口点可能还包含窗体字段、QueryStrings、Cookie、HTTP 标头和 Web 服务参数。</p><p>必须针对模型绑定执行以下输入验证检查：</p><ul><li>应该使用 RegularExpression 批注来批注模型属性，以便接受允许的字符，遵守最大允许长度</li><li>控制器方法应执行 ModelState 有效性检查</li></ul>|

## <a id="richtext"></a>应该针对接受所有字符的窗体字段（例如 RTF 编辑器）应用净化

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [为不安全的输入编码](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3)、[HTML 净化器](https://github.com/mganss/HtmlSanitizer) |
| 步骤 | <p>找到想要使用的所有静态标记。 常见的做法是限制对安全的 HTML 元素设置格式，例如 `<b>`（粗体）和 `<i>`（斜体）。</p><p>写入数据之前，对其进行 HTML 编码。 这样就能使任何恶意脚本变得安全，因为它将会作为文本而不是可执行代码进行处理。</p><ol><li>通过在 @ 页面指令中添加 ValidateRequest="false" 特性来禁用 ASP.NET 请求验证</li><li>使用 HtmlEncode 方法为字符串输入编码</li><li>使用 StringBuilder 并调用其 Replace 方法，有选择性地删除想要允许的 HTML 元素中的编码</li></ol><p>引用中的页面可通过设置 `ValidateRequest="false"` 来禁用 ASP.NET 请求验证。 它会对输入进行 HTML 编码，并有选择性地允许 `<b>` 和 `<i>`。或者，也可以使用 .NET 库进行 HTML 净化。</p><p>HtmlSanitizer 是一个 .NET 库，用于清理可能会导致 XSS 攻击的构造中的 HTML 片段和文档。 它使用 AngleSharp 来分析、处理和呈现 HTML 与 CSS。 可在服务器端以 NuGet 包的形式安装 HtmlSanitizer，通过相关的 HTML 或 CSS 净化方法（如果适用）传递用户输入。 请注意，只有在万不得已的情况下，才应考虑将净化用作安全控制措施。</p><p>输入验证和输出编码被认为是更好的安全控制措施。</p> |

## <a id="inbuilt-encode"></a>不要将 DOM 元素分配到没有内置编码的接收器

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | 许多 Javascript 函数默认不执行编码。 通过此类函数将不受信任的输入分配给 DOM 元素时，可能会导致执行跨站点脚本 (XSS)。| 

### <a name="example"></a>示例
下面是不安全做法的示例： 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
不要使用 `innerHtml`，而是使用 `innerText`。 同样，不要使用 `$("#elm").html()`，而是使用 `$("#elm").text()` 

## <a id="redirect-safe"></a>验证应用程序中的所有重定向是否闭合且安全

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [OAuth 2.0 授权框架 - 开放重定向程序](http://tools.ietf.org/html/rfc6749#section-10.15) |
| 步骤 | <p>要求重定向到用户提供的位置的应用程序设计必须将可能的重定向目标限制为站点或域的预定义“安全”列表。 应用程序中的所有重定向必须闭合且安全。</p><p>为此，请按以下步骤操作：</p><ul><li>识别所有重定向</li><li>针对每个重定向实施适当的缓解措施。 适当的缓解措施包括重定向允许列表或用户确认。 如果包含开放重定向漏洞的某个网站或服务使用 Facebook/OAuth/OpenID 标识提供者，攻击者可以窃取用户的登录令牌该模拟该用户。 使用 RFC 6749“The OAuth 2.0 Authorization Framework”（OAuth 2.0 授权框架）第 10.15 部分“Open Redirects”（开放重定向）中所述的 OAuth 时会固有地存在此风险。同样，鱼叉网络钓鱼攻击可以使用开放重定向窃取用户的凭据</li></ul>|

## <a id="string-method"></a>针对 Controller 方法接受的所有字符串类型参数实施输入验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、MVC5、MVC6 |
| 属性              | 不适用  |
| 参考              | [验证 MVC 应用程序中的模型数据](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)、[ASP.NET MVC 应用程序指导原则](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| 步骤 | 对于只接受基元数据类型而不是模型作为参数的方法而言，应该使用正则表达式执行输入验证。 在此处，应该结合有效的正则表达式模式使用 Regex.IsMatch。 如果输入与指定的正则表达式不匹配，控制不应该继续，而是应该显示有关验证失败的充分警告。| 

## <a id="dos-expression"></a>针对正则表达式处理设置超时上限，防止由于正则表达式错误而出现 DoS

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、Web 窗体、MVC5、MVC6  |
| 属性              | 不适用  |
| 参考              | [DefaultRegexMatchTimeout 属性](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| 步骤 | 为了确保拒绝服务攻击不会错误地创建正则表达式，从而导致大量的回溯，应设置全局默认超时。 如果处理时间超过了定义的上限，将引发超时异常。 如果不进行任何配置，超时将是无限期。| 

### <a name="example"></a>示例
例如，如果处理时间超过 5 秒，以下配置将引发 RegexMatchTimeoutException： 

```C#
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>避免在 Razor 视图中使用 Html.Raw

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | MVC5、MVC6 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | ASP.Net 网页 (Razor) 执行自动 HTML 编码。 嵌入式代码片段（@ 块）打印的所有字符串会自动进行 HTML 编码。 但是，调用 `HtmlHelper.Raw` 方法时，将返回未经 HTML 编码的标记。 如果使用 `Html.Raw()` 帮助器方法，它会绕过 Razor 提供的自动编码保护。|

### <a name="example"></a>示例
下面是不安全做法的示例： 

```C#
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
除非需要显示标记，否则不要使用 `Html.Raw()`。 此方法不会隐式执行输出编码。 应使用其他 ASP.NET 帮助器，例如 `@Html.DisplayFor()` 

## <a id="stored-proc"></a>不要在存储过程中使用动态查询

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | 数据库 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | <p>SQL 注入攻击利用输入验证中的漏洞在数据库中运行任意命令。 如果应用程序使用输入来构造用于访问数据库的动态 SQL 语句，则可能会发生这种攻击。 如果代码使用的存储过程是包含原始用户输入的传递字符串，则也可能会发生这种攻击。 攻击者可以使用 SQL 注入式攻击在数据库中执行任意命令。 所有 SQL 语句（包括存储过程中的 SQL 语句）必须参数化。 参数化 SQL 语句将会正常接受包含对 SQL 具有特殊含义的字符（如单引号），因为这些字符已强类型化。 |

### <a name="example"></a>示例
下面是不安全的动态存储过程的示例： 

```C#
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>示例
下面是安全实现的同一个存储过程： 
```C#
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>确保针对 Web API 方法执行模型验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web API | 
| SDL 阶段               | 构建 |  
| 适用的技术 | MVC5、MVC6 |
| 属性              | 不适用  |
| 参考              | [ASP.NET Web API 中的模型验证](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| 步骤 | 当客户端向 Web API 发送数据时，必须在执行任何处理之前验证该数据。 对于接受模型作为输入的 ASP.NET Web API 而言，请在模型中使用数据注释，以便针对模型属性设置验证规则。|

### <a name="example"></a>示例
以下代码演示了相同的操作： 

```C#
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>示例
在 API 控制器的操作方法中，必须显式验证模型的有效性，如下所示： 

```C#
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>针对 Web API 方法接受的所有字符串类型参数实施输入验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web API | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、MVC 5、MVC 6 |
| 属性              | 不适用  |
| 参考              | [验证 MVC 应用程序中的模型数据](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)、[ASP.NET MVC 应用程序指导原则](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| 步骤 | 对于只接受基元数据类型而不是模型作为参数的方法而言，应该使用正则表达式执行输入验证。 在此处，应该结合有效的正则表达式模式使用 Regex.IsMatch。 如果输入与指定的正则表达式不匹配，控制不应该继续，而是应该显示有关验证失败的充分警告。|

## <a id="typesafe-api"></a>确保在 Web API 中使用类型安全的参数进行数据访问

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web API | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | <p>如果使用 Parameters 集合，SQL 会将输入视为文本值而不是可执行代码。 使用 Parameters 集合可针对输入数据实施类型和长度约束。 如果值超出范围，将触发异常。 如果不使用类型安全的 SQL 参数，攻击者可能会执行嵌入在未筛选输入中的注入式攻击。</p><p>构造 SQL 查询时请使用类型安全的参数，避免未筛选的输入发生 SQL 注入式攻击。 可在存储过程和动态 SQL 语句中使用类型安全的参数。 数据库将参数视为文本值而不是可执行代码。 此外，应检查参数的类型和长度。</p>|

### <a name="example"></a>示例
以下代码演示在调用存储过程时，如何对 SqlParameterCollection 使用类型安全的参数。 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
在上面的代码示例中，输入值不能超过 11 个字符。 如果数据不符合该参数定义的类型或长度，SqlParameter 类将引发异常。 

## <a id="sql-docdb"></a>对 Cosmos DB 使用参数化 SQL 查询

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Azure Document DB | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [宣布推出 Cosmos DB 中的 SQL 参数化](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| 步骤 | 尽管 Cosmos DB 仅支持只读查询，但如果查询是通过串联用户输入构造的，则仍有可能会发生 SQL 注入。 用户可能会通过编写恶意 SQL 查询，获取对同一集合中他们本应无权访问的数据的访问权限。 如果查询是基于用户输入构造的，请使用参数化 SQL 查询。 |

## <a id="schema-binding"></a>通过架构绑定执行 WCF 输入验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | WCF | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、NET Framework 3 |
| 属性              | 不适用  |
| 参考              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| 步骤 | <p>缺少验证会导致不同类型的注入式攻击。</p><p>消息验证相当于 WCF 应用程序的一道防线。 借助这种方式，可以使用架构来验证消息，防止 WCF 服务操作受到恶意客户端的攻击。 验证客户端收到的所有消息，防止客户端受到恶意服务的攻击。 当操作使用消息约定或数据约定时，可以使用消息验证来验证消息，而使用参数验证则做不到这一点。 使用消息验证可在架构中创建验证逻辑，从而提供更高的灵活性，缩短开发时间。 架构可在组织内的不同应用程序之间重复使用，为数据表示建立标准。 此外，当操作使用涉及到表示业务逻辑的约定的更复杂数据类型时，可以使用消息验证来保护这些操作。</p><p>若要执行消息验证，首先需要构建一个架构来表示服务的操作，以及这些操作使用的数据类型。 然后创建一个 .NET 类，用于实现自定义客户端消息检查器和自定义调度程序消息检查器，以验证与服务之间相互发送/接收的消息。 接下来，实现自定义的终结点行为，在客户端和服务中启用消息验证。 最后，在类中实现一个自定义配置元素，以便在服务或客户端的配置文件中公开扩展的自定义终结点行为</p>|

## <a id="parameters"></a>WCF - 通过参数检查器执行输入验证

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | WCF | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型、NET Framework 3 |
| 属性              | 不适用  |
| 参考              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| 步骤 | <p>输入和数据验证相当于 WCF 应用程序的一道重要防线。 应该验证 WCF 服务操作中公开的所有参数，防止服务受到恶意客户端的攻击。 反之，也应该验证客户端收到的所有返回值，防止客户端受到恶意服务的攻击</p><p>WCF 提供不同的扩展点用于通过创建自定义扩展来自定义 WCF 运行时行为。 消息检查器和参数检查器是两个扩展机制，用于针对在客户端与服务之间传递的数据实施更大力度的控制。 应该为输入验证使用参数检查器；仅当需要检查流入和流出服务的整个消息时，才使用消息检查器。</p><p>若要执行输入验证，需要生成一个 .NET 类并实现自定义参数检查器，用于验证服务中操作上的参数。 然后，需要实现自定义的终结点行为，在客户端和服务中启用验证。 最后，需要在类中实现一个自定义配置元素，以便在服务或客户端的配置文件中公开扩展的自定义终结点行为</p>|

