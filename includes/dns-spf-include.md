发送方策略框架 (SPF) 记录用于指定允许代表给定域名发送电子邮件的电子邮件服务器。  正确配置 SPF 记录非常重要，可防止收件人将邮件标记为“垃圾邮件”。

DNS RFC 最初引入了新的“SPF”记录类型来支持这种方案。 为了支持旧名称服务器，还允许使用 TXT 记录类型指定 SPF 记录。  这种不明确性导致混乱，已通过 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) 得到解决。  其中规定：应仅使用 TXT 记录类型创建 SPF 记录，并弃用 SPF 记录类型。

**SPF 记录受 Azure DNS 支持且应使用 TXT 记录类型创建。** 不支持已过时的 SPF 记录类型。 [导入 DNS 区域文件](../articles/dns/dns-import-export.md)时，使用 SPF 记录类型的任何 SPF 记录将转换为 TXT 记录类型。