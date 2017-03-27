
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>常见问题 - Azure 分配的 IP 地址的反向 DNS

### <a name="how-much-do-reverse-dns-records-cost"></a>反向 DNS 记录的费用如何？

完全免费！  反向 DNS 记录或查询不额外收取费用。

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>是否从 Internet 解析 Azure 分配的公共 IP 地址的反向 DNS 记录？

 是的。 为公共 IP 地址设置反向 DNS 属性后，Azure 将管理所有必要的 DNS 委托和 DNS 区域，确保可为所有 Internet 用户解析反向 DNS 记录。

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>是否为公共 IP 地址创建默认的反向 DNS 记录？

不能。 反向 DNS 是可选功能。 如果选择不配置反向 DNS，则不会创建任何默认的反向 DNS 记录。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完全限定的域名 (FQDN) 的格式是什么？

FQDN 以正向顺序指定，必须以点号结尾（例如，“app1.contoso.com.”）。

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>指定的反向 DNS 验证检查为何失败？

如果反向 DNS 的验证检查失败，服务管理操作也会失败。 请根据需要更正反向 DNS 值，然后重试。

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>是否可以管理 Azure 网站的反向 DNS？

Azure 网站不支持反向 DNS。 Azure 虚拟机支持反向 DNS。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>是否可以针对公共 IP 地址配置多个反向 DNS 记录？

不能。 Azure 仅支持对每个公共 IP 地址配置一个反向 DNS 记录。 但是，每个公共 IP 地址可以有自身的反向 DNS 记录。

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>是否可以针对 IPv6 公共 IP 地址配置反向 DNS 记录？

不能。  目前，只有 IPv4 公共 IP 地址支持反向 DNS 记录。

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>在不指定 DomainNameLabel 的情况下是否可以针对公共 IP 地址配置反向 DNS 记录？

不能。 若要针对公共 IP 地址使用反向 DNS 记录，必须指定 DomainNameLabel 属性。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>是否可以从 Azure 计算服务将电子邮件发送到外部域？

不能。 [Azure 计算服务不支持向外部域发送电子邮件](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。


<!--HONumber=Nov16_HO3-->


