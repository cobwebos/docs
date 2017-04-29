
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>常见问题 - 在 Azure DNS 中托管 ARPA 区域

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>是否可以在 Azure DNS 上托管 ISP 分配的 IP 块的 ARPA 区域？

是的。 完全支持在 Azure DNS 托管你自己的 IP 范围的 ARPA 区域。

只需[在 Azure DNS 中创建该区域](../articles/dns/dns-getstarted-create-dnszone.md)，并咨询 ISP 来[委托该区域](../articles/dns/dns-domain-delegation.md)。  然后，便可以像处理其他记录类型一样，管理每个反向查找的 PTR 记录。

也可以[使用 Azure CLI 导入现有的反向查找区域](../articles/dns/dns-import-export.md)。

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>托管 ARPA 区域的费用如何？

在 Azure DNS 中托管 ISP 分配的 IP 块的 ARPA 区域根据[标准 Azure DNS 费率](https://azure.microsoft.com/pricing/details/dns/)收费。

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>是否可以在 Azure DNS 中托管 IPv4 和 IPv6 地址的 ARPA 区域？

是的。
