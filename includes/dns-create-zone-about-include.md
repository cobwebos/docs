DNS 区域用来托管某个特定域的 DNS 记录。 需要创建 DNS 区域才能开始托管域。 为特定域创建的任何 DNS 记录都将位于该域的 DNS 区域内部。

例如，域“contoso.com”可能包含许多 DNS 记录，例如“mail.contoso.com”（用于邮件服务器）和“www.contoso.com”（用于网站）。

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>关于 DNS 区域名称
* 在资源组中，区域名称必须是唯一的，不能存在该域。 否则，操作将失败。
* 同一区域名称可重复用于不同的资源组或不同的 Azure 订阅。
* 如果多个区域共享同一个名称，每个实例都将分配到不同的名称服务器地址，而且只能有一个实例从父域委派。 有关详细信息，请参阅 [向 Azure DNS 委派域](../articles/dns/dns-domain-delegation.md)。


<!--HONumber=Nov16_HO2-->


