发送方策略框架 (SPF) 记录用于指定可以代表域名发送电子邮件的电子邮件服务器。 正确配置 SPF 记录非常重要，可防止收件人将你的电子邮件标记为“垃圾邮件”。

DNS RFC 最初引入了新的 SPF 记录类型来支持此方案。 为了支持旧名称服务器，还允许它们使用 TXT 记录类型指定 SPF 记录。 这种不明确性导致混乱，已通过 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) 得到解决。 它指出必须使用 TXT 记录类型创建 SPF 记录。 它还指出 SPF 记录类型已弃用。

**SPF 记录受 Azure DNS 支持且必须使用 TXT 记录类型创建。** 不支持已过时的 SPF 记录类型。 [导入 DNS 区域文件](../articles/dns/dns-import-export.md)时，使用 SPF 记录类型的任何 SPF 记录将转换为 TXT 记录类型。
