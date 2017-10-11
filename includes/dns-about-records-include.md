### <a name="record-names"></a>记录名称

在 Azure DNS 中，通过使用相对名称指定记录。 A*完全限定的*域名 (FQDN) 包括区域名称，而*相对*名称却没有。 例如，记录的相对名称 www 区域 contoso.com 中会提供完全限定的记录名称 www.contoso.com。

*顶点*记录是根处的 DNS 记录 (或*顶点*) 的 DNS 区域。 例如，在 DNS 区域 contoso.com，顶点记录还具有 contoso.com 的完全限定的名称 (这有时称为*裸*域)。  按照约定的相对名称 @ 用于表示顶点记录。

### <a name="record-types"></a>记录类型

每个 DNS 记录具有一个名称和类型。 记录被组织到根据它们所包含的数据的各种类型。 最常用的类型是一个 A 记录，映射到 IPv4 地址的名称。 另一种常见类型是 MX 记录，映射到邮件服务器的名称。

Azure DNS 支持所有常见的 DNS 记录类型： A、 AAAA、 CNAME、 MX、 NS、 PTR、 SOA、 SRV 和 TXT。 请注意， [SPF 记录表示使用 TXT 记录](../articles/dns/dns-zones-records.md#spf-records)。

### <a name="record-sets"></a>记录集

有时，你需要创建具有给定名称和类型的多个 DNS 记录。 例如，假设两个不同的 IP 地址上托管 www.contoso.com web 站点。 网站需要两个不同 A 记录，另一个用于每个 IP 地址。 下面是记录集的示例：

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS 管理使用的所有 DNS 记录*记录集*。 记录集 (也称为*资源*记录集) 的区域中的 DNS 记录，具有相同的名称均为相同类型的集合。 大多数的记录集包含单个记录。 但是，上面所示的示例在一个记录集包含多个记录，并不是少见。

例如，假设你已在区域 'contoso.com' 中创建 A 记录 www，指向该 IP 地址"134.170.185.46"（上面第一条记录）。  若要创建第二个记录您会将该记录添加到现有的记录集，而不创建其他的记录集。

SOA 和 CNAME 记录类型是异常。 DNS 标准不允许多个具有相同的名称用于这些类型记录，因此这些记录集只能包含单个记录。