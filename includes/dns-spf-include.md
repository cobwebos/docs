发送方策略框架 (SPF) 记录用于指定允许哪些电子邮件服务器发送代表一个给定的域名的电子邮件。  正确配置的 SPF 记录务必防止标记为垃圾邮件你的电子邮件的收件人。

DNS Rfc 最初引入了新的 SPF 记录类型，以支持此方案。 若要支持旧名称服务器，它们还允许使用 TXT 记录类型，指定 SPF 记录。  这种混乱情况就会导致混淆，通过解决[RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1)。  此状态时，仅应使用 TXT 记录类型，创建 SPF 记录和 SPF 记录类型已弃用。

**SPF 记录由 Azure DNS 支持，且应使用 TXT 记录类型来创建。** 不支持已过时的 SPF 记录类型。 当[导入 DNS 区域文件](../articles/dns/dns-import-export.md)，使用 SPF 记录类型的任何 SPF 记录转换为 TXT 记录类型。