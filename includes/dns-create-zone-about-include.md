DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。

例如，域“contoso.com”可能包含几条 DNS 记录，如“mail.contoso.com”（用于邮件服务器）和“www.contoso.com”（用于网站）。

在 Azure DNS 中创建 DNS 区域时：

* 在资源组中，区域名称必须是唯一的，不能存在该域。 否则，操作会失败。
* 可在不同资源组或不同 Azure 订阅中重复使用同一区域名称。
* 当多个区域共享相同的名称时，将为每个实例分配不同的名称服务器地址。 使用域名注册机构仅可配置一组地址。

> [!NOTE]
> 不必拥有域名即可在 Azure DNS 中以该域名创建 DNS 区域。 但是，需要拥有域才能通过域名注册机构将 Azure DNS 名称服务器配置为域名的正确名称服务器。
> 
> 有关详细信息，请参阅 [向 Azure DNS 委派域](../articles/dns/dns-domain-delegation.md)。