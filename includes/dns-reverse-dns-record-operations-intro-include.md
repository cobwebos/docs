## <a name="what-is-reverse-dns"></a>什么是反向 DNS？

传统的 DNS 记录可将 DNS 名称（例如“www.contoso.com”）映射为 IP 地址（例如 64.4.6.100）。  反向 DNS 可将 IP 地址 (64.4.6.100) 转换回名称 (www.contoso.com)。

反向 DNS 记录可在多种场合下使用。 例如，反向 DNS 记录可以验证电子邮件的发件人，因此广泛用于防御垃圾电子邮件。  接收方邮件服务器将检索发送方服务器 IP 地址的反向 DNS 记录，验证该主机是否有权从来源域发送电子邮件。 （但请注意，[Azure 计算服务不支持向外部域发送电子邮件](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。）

## <a name="how-reverse-dns-works"></a>反向 DNS 的工作原理

反向 DNS 记录托管在名为“ARPA”区域的特殊 DNS 区域中。  这些区域构成了一个独立的 DNS 层次结构，这种结构与托管类似于“contoso.com”的域的正常层次结构是并列关系。

例如，DNS 记录“www.contoso.com”是使用区域“contoso.com”中名称为“www”的 DNS“A”记录实现的。  此 A 记录指向相应的 IP 地址，在本例中为 64.4.6.100。  反向查找是单独使用区域“6.4.64.in-addr.arpa”（请注意 ARPA 区域中的 IP 地址是反向的）中名为“100”的“PTR”记录实现的。如果配置正确，此 PTR 记录将指向名称“www.contoso.com”。

如果组织被分配了 IP 地址块，则还有权管理相应的 ARPA 区域。 对应于 Azure 使用的 IP 地址块的 ARPA 区域由 Microsoft 托管和管理。 ISP 可以代你托管你自己的 IP 地址的 ARPA 区域，或者允许你在所选的 DNS 服务（例如 Azure DNS）中托管 ARPA 区域。

> [!NOTE]
> 正向 DNS 查找和反向 DNS 查找是在独立的并行 DNS 层次结构中实现的。 “www.contoso.com”的反向查找**不是**托管在区域“contoso.com”中，而是托管在相应 IP 地址块的 ARPA 区域中。

有关反向 DNS 的详细信息，请参阅 [Reverse DNS Lookup](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)（反向 DNS 查找）。

## <a name="azure-support-for-reverse-dns"></a>Azure 对反向 DNS 的支持

在反向 DNS 方面，Azure 支持两种不同的方案：

1. 托管对应于 IP 地址块的 ARPA 区域。
2. 允许用户为分配给 Azure 服务的 IP 地址配置反向 DNS 记录。

为了支持前一种方案，可以使用 Azure DNS 托管 ARPA 区域，管理每个反向 DNS 查找的 PTR 记录。  创建 ARPA 区域、设置委托和配置 PTR 记录的过程与常规 DNS 区域相同。  唯一的差别在于，必须通过 ISP 而不是 DNS 注册机构配置委托，并且只能 PTR 记录类型。

为了支持后一种方案，可以使用 Azure 为分配给服务的 IP 地址配置反向查找。  Azure 将这种反向查找配置为相应 ARPA 区域中的 PTR 记录。  这些对应于 Azure 使用的所有 IP 范围的 ARPA 区域由 Microsoft 托管。 **本文的余下内容详细描述了此方案。**


<!--HONumber=Nov16_HO3-->


