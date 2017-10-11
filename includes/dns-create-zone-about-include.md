DNS 区域用于承载特定域的 DNS 记录。 若要启动托管在 Azure DNS 中的域，你需要创建该域名的 DNS 区域。 此 DNS 区域内然后创建你的域每个 DNS 记录。

例如，contoso.com 的域可能包含多个 DNS 记录，如 mail.contoso.com （适用于邮件服务器） 和 www.contoso.com （适用于网站）。

当在 Azure DNS 中创建 DNS 区域：

* 区域的名称必须是唯一在资源组中，并且必须已存在该区域。 否则，该操作将失败。
* 在不同的资源组或不同的 Azure 订阅，可以重复使用相同的区域名称。
* 多个区域共享相同的名称，其中每个实例分配不同的名称服务器地址。 可以使用的域名注册机构配置仅一组地址。

> [!NOTE]
> 无需拥有要创建使用 Azure DNS 中该域名的 DNS 区域的域名。 但是，你需要拥有要将 Azure DNS 名称服务器配置为使用域注册机构的域名的正确名称服务器的域。
> 
> 有关详细信息，请参阅[委托到 Azure DNS 域](../articles/dns/dns-domain-delegation.md)。