### <a name="record-names"></a>记录名称

在 Azure DNS 中，记录使用相对名称指定。 完全限定的域名 (FQDN) 包括区域名称，而相对域名则不包括。 例如，“contoso.com”区域中的相对记录名称“www”会提供完全限定的记录名称“www.contoso.com”。

顶点记录是位于 DNS 区域的根（或顶点）中的 DNS 记录。 例如，在 DNS 区域“contoso.com”中，顶点记录还具有完全限定的名称“contoso.com”（有时称为裸域）。  按照惯例，相对名称 @ 用于表示顶点记录。

### <a name="record-types"></a>记录类型

每个 DNS 记录都有一个名称和类型。 这些记录根据其所包含的数据分为各种类型。 最常见的类型为“A”记录，这种记录将名称映射到 IPv4 地址。 另一种常见类型是“MX”记录，这种记录将名称映射到邮件服务器。

Azure DNS 支持所有常见 DNS 记录类型：A、AAAA、CAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT。 请注意，[SPF 记录使用 TXT 记录表示](../articles/dns/dns-zones-records.md#spf-records)。

### <a name="record-sets"></a>记录集

有时，需要创建具有给定名称和类型的多个 DNS 记录。 例如，假设在两个不同的 IP 地址上托管“www.contoso.com”网站。 该网站需要两个不同的 A 记录，每个 IP 地址一个。 这就是记录集的示例：

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS 使用记录集管理所有 DNS 记录。 记录集（也称为资源记录集）是某个区域中具有相同名称、相同类型的 DNS 记录的集合。 大多数记录集包含单个记录。 但是，上面所示的示例一个记录集包含多个记录，这并不少见。

例如，假设已在区域“contoso.com”中创建 A 记录“www”，指向 IP 地址“134.170.185.46”（上述第一条记录）。  要创建第二条记录，应将此记录添加到现有记录集而非创建其他记录集。

SOA 和 CNAME 记录类型例外。 对于这些类型，DNS 标准不允许多个记录具有相同的名称，因此这些记录集仅可包含单个记录。