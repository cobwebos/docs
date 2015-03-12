<properties title="了解如何将 Azure 网站配置为使用自定义域名" pageTitle="为 Azure 网站配置自定义域名" metaKeywords="Azure, Azure  Websites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 为 Azure 网站配置自定义域名

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">自定义域</a><a href="/zh-cn/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-cn/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-cn/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-cn/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-cn/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-cn/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-cn/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-cn/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-cn/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">网站</a> | <a href="/zh-cn/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用 Traffic Manager 的网站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

当你创建网站时，Azure 会将其分配给 azurewebsites.net 的子域。例如，如果你的网站名为 **contoso**，URL 为 **contoso.chinacloudsites.cn**。Azure 还会将分配一个虚拟 IP 地址。

![contoso.chinacloudsites.cn subdomain][subdomain]

对于生产网站，你可能希望用户看到的自定义域名称。本文介绍如何使用 Azure 网站配置自定义域。（这篇文章提供有关任何域注册机构的一般说明。在这篇文章顶部的选项卡将链接到一些文章为特定的注册机构。）

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文内容：


-   [概述]
-   [DNS 记录类型]
-   [查找虚拟 IP 地址]
-   [创建 DNS 记录]
-   [创建"awverify"记录（仅记录）](#awverify)
-   [在网站上启用域名]


## 概述

以下是配置自定义域名的常规步骤：

1. 保留你的域名。本文不涉及该过程。有很多域注册机构可供选择。当你注册时，其站点将引导你完成该过程。
1. 创建将域映射到你的 Azure 网站的 DNS 记录。 
1. 在 Azure 管理门户中添加域名。 

在此基本大纲中，有一些需要注意的特定情况：

- 映射根域。根域是你通过域注册机构保留的域。例如 **contoso.com**。
- 映射子域。例如 **blogs.contoso.com**。可以将不同的子域映射到不同的网站。
- 映射通配符。例如 **\*.contoso.com**。通配符条目会应用到域的所有子域。 
 
[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## DNS 记录类型

域名系统 (DNS) 使用数据记录将域名映射到 IP 地址。有几种类型的 DNS 记录。对于网站，将创建 *A* 记录或 *CNAME* 记录。

- A**（地址）**记录会将域名映射至 IP 地址。 
- **CNAME（规范名称）**记录会将域名映射至其他域名。DNS 使用第二个名称来查找地址。用户仍然可以在浏览器中看到第一个域名。例如，你可以将 contoso.com 映射至 *&lt;yoursite&gt;*.chinacloudsites.cn。 

如果 IP 地址更改，CNAME 条目仍然有效，但 A 记录就必须更新。不过，有些域注册机构不允许 CNAME 记录使用根域或通配符域。在这种情况下，必须使用 A 记录。 

> [AZURE.NOTE] 如果你删除网站后再重新创建，或是将网站模式更改回免费，则 IP 地址可能会更改。


## 查找虚拟 IP 地址 

如果你要创建 CNAME 记录，可跳过此步骤。若要创建 A 记录，你需要网站的虚拟 IP 地址。获取该 IP 地址：

1.	在你的浏览器中，打开 [Azure 管理门户](https://manage.windowsazure.cn)。
2.	在"网站"选项卡中，单击站点的名称并选择"仪表板"。
3.	从页面底部选择"管理域"。（如果此选项已禁用，请确保你使用的模式为"共享"、"基本"或"标准"。有关详细信息，请参阅[如何缩放网站](http://www.windowsazure.cn/zh-cn/documentation/articles/web-sites-scale/)。） 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	IP 地址列在接近对话框底部的地方。

	![](media/web-sites-custom-domain-name/ipaddress.png)

## 创建 DNS 记录

登录域注册机构，并使用他们的工具添加 A 记录或 CNAME 记录。每个注册机构的网站都会稍有不同，但以下是一些通用准则。 

1.	查找管理 DNS 记录的页面。查找网站中标签为"域名"、"DNS"或"名称服务器管理"的链接或区域。通过查看你的帐户信息，然后找一个"我的域"这样的链接，通常可以找到访问链接。
2.	找到管理页后，请查找可让你添加或编辑 DNS 记录的链接。此链接可能是作为"区域文件"、"DNS 记录"或"高级"配置链接列出。

页面可能会分别列出 A 记录与 CNAME 记录，或提供选择记录类型的下拉列表。此外，页面也可能会使用其他名称做为记录类型，例如 **IP 地址记录**而不是 A 记录，或使用**别名记录**而不是 CNAME 记录。注册机构通常会为你创建一些记录，所以可能已经有根域或一般子域的记录，例如 **www**。

当你创建或编辑记录时，可使用字段将域名映射至 IP 地址（针对 A 记录），或映射至其他域（针对 CNAME 记录）。对于 CNAME 记录，你要 *from*自定义域映射 *to* chinacloudsites.cn 子域。 

在许多注册机构的工具中，你只要输入域的子域部分即可，不必输入整个域名。此外，许多工具都会使用"@"表示根域。例如：

<table cellspacing="0" border="1">
<tr>
	<th>Host</th>
	<th>记录类型</th>
	<th>IP 地址或 URL</th>
</tr>
<tr>
	<td>@</td>
	<td>A（地址）</td>
	<td>127.0.0.1</td>
</tr>
<tr>
	<td>www</td>
	<td>CNAME（别名）</td>
	<td>contoso.chinacloudsites.cn</td>
</tr>
</table>

假设自定义域名为 contoso.com，则会创建以下记录：

- **contoso.com** 已映射到 127.0.0.1。
- **www.contoso.com** 已映射到 **contoso.chinacloudsites.cn**。


<h2 id="awverify">创建"awverify"记录（仅记录）</h2>

如果创建 A 记录，Azure 网站也需要特殊的 CNAME 记录，用于确认你是否拥有尝试要使用的域。此 CNAME 记录必须采用以下形式。 

- *如果 A 记录映射根域或通配符域：* 请创建从 **awverify.&lt;yourdomain&gt;** 映射至 **awverify.&lt;yourWebsitename&gt;.chinacloudsites.cn** 的 CNAME 记录。例如，针对 **contoso.com** 的 A 记录，创建 **awverify.contoso.com** 的 CNAME 记录。
- *如果 A 记录映射特定子域：* 请创建从 **awverify.&lt;subdomain&gt;** 映射至 **awverify.&lt;your Websitename&gt;.chinacloudsites.cn** 的 CNAME 记录。例如，针对 **blogs.contoso.com** 的 A 记录，创建 **awverify.blogs.contoso.com** 的 CNAME 记录。

你网站的访客不会看到 awverify 子域；这只是供 Azure 用于验证域。

## 在网站上启用域名

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[概述]: #overview
[DNS 记录类型]: #dns-record-types
[查找虚拟 IP 地址]: #find-the-virtual-ip-address
[创建 DNS 记录]: #create-the-dns-records
[在网站上启用域名]: #enable-the-domain-name-on-your-website

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
