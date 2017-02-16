# <a name="configuring-a-custom-domain-name-for-an-azure-website"></a>为 Azure 网站配置自定义域名
创建网站时，Azure 会提供 azurewebsites.net 域的友好子域，以便用户可以使用类似于 http://&lt;mysite>.azurewebsites.net 的 URL 访问该网站。 但是，如果将网站配置为共享或标准模式，则可以将网站映射到您自己的域名。

另外，您可以使用 Azure Traffic Manager 对您的网站的传入流量实现负载平衡。 有关流量管理器在网站中的工作原理的详细信息，请参阅[使用 Azure 流量管理器控制 Azure 网站流量][trafficmanager]。

> [!NOTE]
> 本任务中的过程适用于 Azure 网站；对于云服务，请参阅<a href="/develop/net/common-tasks/custom-dns/">在 Azure 中配置自定义域名</a>。
> 
> [!NOTE]
> 本任务中的步骤要求您将网站配置为共享或标准模式，这可能会更改对您的订阅的计费量。 有关详细信息，请参阅<a href="/pricing/details/web-sites/">网站定价详细信息</a>。
> 
> 

本文内容：

* [了解 CNAME 和 A 记录](#understanding-records)
* [将网站配置为共享或标准模式](#bkmk_configsharedmode)
* [将网站添加到流量管理器](#trafficmanager)
* [为自定义域添加 CNAME](#bkmk_configurecname)
* [为自定义域添加 A 记录](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>了解 CNAME 和 A 记录</h2>

CNAME（即别名记录）和 A 记录都允许您将域名与网站进行关联，但是，它们的工作方式不同。

### <a name="cname-or-alias-record"></a>CNAME 记录，或称别名记录
CNAME 记录将*特定*域（例如 **contoso.com** 或 **www.contoso.com**）映射到规范域名。 在这种情况下，规范域名是 Azure 网站的 **&lt;myapp>.azurewebsites.net** 域名，或流量管理器配置文件的 **&lt;myapp>.trafficmgr.com** 域名。 创建后，CNAME 将为 **&lt;myapp>.azurewebsites.net** 或 **&lt;myapp>.trafficmgr.com** 域名创建一个别名。 CNAME 条目将自动解析为 **&lt;myapp>.azurewebsites.net** 或 **&lt;myapp>.trafficmgr.com** 域名的 IP 地址，因此，如果网站的 IP 地址发生更改，你不需要采取任何措施。

> [!NOTE]
> 某些域注册机构只允许在使用 CNAME 记录（例如 www.contoso.com）时映射子域，而不允许在使用根名称（例如 contoso.com）时映射。 有关 CNAME 记录的详细信息，请参阅由你的注册机构提供的文档、<a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME 记录上的 Wikipedia 条目</a>或 <a href="http://tools.ietf.org/html/rfc1035">IETF 域名 - 实现和规范</a>文档。
> 
> 

### <a name="a-record"></a>A 记录
A 记录将域（例如 **contoso.com** 或 **www.contoso.com**）*或通配符域*（例如 **\*.contoso.com**）映射到 IP 地址。 对于 Azure 网站而言，这是服务的虚拟 IP 或者您为网站购买的具体 IP 地址。 与 CNAME 记录相比，A 记录的主要优势是用户可持有使用通配符的条目，例如 ***.contoso.com**，用于处理多个子域（例如 **mail.contoso.com**、**login.contoso.com** 或 **www.contso.com**）的请求。

> [!NOTE]
> 由于 A 记录映射到静态 IP 地址，因此它无法自动解析网站的 IP 地址的更改。 用于 A 记录的 IP 地址是您为网站配置自定义域名设置时提供的；但是，如果您删除并重新创建了网站或者将网站模式改回了免费，则该值可能会更改。
> 
> [!NOTE]
> A 记录不能用于使用 Traffic Manager 的负载平衡。 有关详细信息，请参阅[使用 Azure 流量管理器控制 Azure 网站流量][trafficmanager]。
> 
> 

<a name="bkmk_configsharedmode"></a><h2>将网站配置为共享或标准模式</h2>

在网站上设置自定义域名只适用于 Azure 网站的共享和标准模式。 将网站从免费网站模式切换到共享或标准网站模式之前，必须先取消网站订阅已有的支出上限。 有关共享和标准模式定价的详细信息，请参阅[定价详细信息][PricingDetails]。

1. 在浏览器中，打开[管理门户][portal]。
2. 在“网站”选项卡中，单击站点的名称。
   
    ![][standardmode1]
3. 单击“缩放”选项卡。
   
    ![][standardmode2]
4. 在“常规”部分中，通过单击“共享”设置网站模式。
   
    ![][standardmode3]
   
   > [!NOTE]
   > 如果您将为此网站使用 Traffic Manager，则必须选择标准模式而非共享模式。
   > 
   > 
5. 单击“保存” 。
6. 在系统提示共享模式（如果选择“标准”，则此处为标准模式）的成本会增加时，如果同意条款，请单击“是”。
   
    <!--![][standardmode4]-->
   
    **注意**<br />
    如果出现“为网站‘网站名称’配置规模失败”错误，可以使用详细信息按钮获得详细信息。

<a name="trafficmanager"></a><h2>（可选）将您的网站添加到 Traffic Manager</h2>

如果希望为网站使用 Traffic Manager，请执行下列步骤。

1. 如果没有流量管理器配置文件，请根据[使用“快速创建”创建流量管理器配置文件][createprofile]中的信息创建一个。 记下与流量管理器配置文件关联的 **.trafficmgr.com** 域名。 这将在后面的步骤中使用。
2. 根据[添加或删除终结点][addendpoint]中的信息在流量管理器配置文件中将网站添加为终结点。
   
   > [!NOTE]
   > 如果在添加终结点时您的网站未列出，请验证是否已将其配置为标准模式。 必须将网站配置为标准模式，Traffic Manager 才起作用。
   > 
   > 
3. 登录到您的 DNS 注册机构的网站，然后转至用于管理 DNS 的页面。 查找网站中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。
4. 现在找到您可以在其中选择或输入 CNAME 记录的位置。 可能需要从下拉列表中选择记录类型，或者需要转到高级设置页面。 应查找 **CNAME**、**别名**或**子域**字样。
5. 还必须为 CNAME 提供域或子域别名。 例如，如果想要为 **www.customdomain.com** 创建别名，则输入 **www**。
6. 还必须提供作为此 CNAME 别名的规范域名的主机名。 这是网站的 **.trafficmgr.com** 名称。

例如，以下 CNAME 记录会将 **www.contoso.com** 的全部流量都转发到 **contoso.trafficmgr.com**（网站的域名）：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>别名/主机名/子域</strong></td>
<td><strong>规范域</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

**www.contoso.com** 的访问者将从不会看到真正的主机(contoso.azurewebsite.net)，因此，最终用户看不到转发过程。

> [!NOTE]
> 如果为网站使用流量管理器，则不需要执行下面的部分（“**为自定义域添加 CNAME**”和“**为自定义域添加 A 记录**”）中的步骤。 在前面的步骤中创建的 CNAME 记录会将传入流量路由到 Traffic Manager，然后后者会将流量路由到网站终结点。
> 
> 

<a name="bkmk_configurecname"></a><h2>为自定义域添加 CNAME</h2>

若要创建 CNAME 记录，必须使用您的注册机构提供的工具在 DNS 表中为您的自定义域添加一个新条目。 每个注册机构指定 CNAME 记录的方法类似但略有不同，但概念是相同的。

1. 使用以下方法之一查找分配给网站的 **.azurewebsite.net** 域名。
   
   * 登录到 [Azure 管理门户][portal]，选择你的网站，选择“仪表板”，然后在“速览”部分中找到“站点 URL”条目。
   * 安装并配置 [Azure Powershell](/manage/install-and-configure-windows-powershell/)，然后使用以下命令：
     
           get-azurewebsite yoursitename | select hostnames
   * 安装并配置 [Azure 命令行接口](/manage/install-and-configure-cli/)，然后使用以下命令：
     
           azure site domain list yoursitename
     
     保存此 **.azurewebsite.net** 名称，因为在后续步骤中将要使用该名称。
2. 登录到您的 DNS 注册机构的网站，然后转至用于管理 DNS 的页面。 查找网站中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。
3. 现在找到您可以在其中选择或输入 CNAME 记录的位置。 可能需要从下拉列表中选择记录类型，或者需要转到高级设置页面。 应查找 **CNAME**、**别名**或**子域**字样。
4. 还必须为 CNAME 提供域或子域别名。 例如，如果想要为 **www.customdomain.com** 创建别名，则输入 **www**。 如果希望为根域创建别名，它可能在注册机构的 DNS 工具中以符号“**@**”的形式列出。
5. 还必须提供作为此 CNAME 别名的规范域名的主机名。 这是网站的 **.azurewebsite.net** 名称。

例如，以下 CNAME 记录会将 **www.contoso.com** 的全部流量都转发到 **contoso.azurewebsite.net**（网站的域名）：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>别名/主机名/子域</strong></td>
<td><strong>规范域</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

**www.contoso.com** 的访问者将从不会看到真正的主机(contoso.azurewebsite.net)，因此，最终用户看不到转发过程。

> [!NOTE]
> 上述示例仅适用于 **www** 子域的流量。 因为无法为 CNAME 记录使用通配符，所以必须为每个域/子域创建一个 CNAME。 如果想要将子域（例如 *.contoso.com）的流量定向到 azurewebsite.net 地址，则可以在 DNS 设置中配置“**URL 重定向**”或“**URL 转发**”条目，或者创建一条 A 记录。
> 
> [!NOTE]
> CNAME 通过 DNS 系统向外传播可能需要一段时间。 直到 CNAME 已传播出去，才能设置网站的 CNAME。 可使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 等服务验证该 CNAME 是否可用。
> 
> 

### <a name="add-the-domain-name-to-your-website"></a>将域名添加到网站
在域名的 CNAME 记录已传播后，必须将其与您的网站关联。 可以使用 Azure 命令行接口 (Azure CLI) 或 Azure 管理门户将 CNAME 记录定义的自定义域名添加到网站。

**使用命令行工具添加域名**

安装并配置 [Azure 命令行接口](/manage/install-and-configure-cli/)，然后使用以下命令：

    azure site domain add customdomain yoursitename

例如，以下命令会将 **www.contoso.com** 的自定义域名添加到 **contoso.azurewebsite.net** 网站：

    azure site domain add www.contoso.com contoso

可以使用以下命令确认自定义域名是否已添加到网站：

    azure site domain list yoursitename

此命令返回的列表应当包含自定义域名以及默认的 **.azurewebsite.net** 条目。

**使用 Azure 管理门户添加域名**

1. 在浏览器中打开 [Azure 管理门户][portal]。
2. 在“网站”选项卡中，单击你的站点名称，选择“仪表板”，然后从页面底部选择“管理域”。
   
    ![][setcname2]
3. 在“域名”文本框中，键入已配置的域名。
   
    ![][setcname3]
4. 单击复选标记以接受该域名。

完成配置后，自定义域名将在网站的“配置”页的“域名”部分中列出。

<a name="bkmk_configurearecord"></a><h2>为自定义域添加 A 记录</h2>

若要创建 A 记录，必须首先找到您的网站的 IP 地址。 然后，使用您的注册机构提供的工具在 DNS 表中为您的自定义域添加一个条目。 每个注册机构指定 A 记录的方法类似但略有不同，但概念是相同的。 除了创建 A 记录之外，还必须创建 Azure 用来验证 A 记录的 CNAME 记录。

1. 在浏览器中打开 [Azure 管理门户][portal]。
2. 在“网站”选项卡中单击你的站点名称，选择“仪表板”，然后从屏幕底部选择“管理域”。
   
    ![][setcname2]
3. 在“管理自定义域”对话框中，找到“在配置 A 记录时要使用的 IP 地址”。 复制 IP 地址。 在创建 A 记录时将使用该地址。
4. 在“管理自定义域”对话框中，记下位于对话框顶部文本末尾的 awverify 域名。 它应是 **awverify.mysite.azurewebsites.net**，其中 **mysite** 是网站的名称。 复制该域名，因为在创建验证用的 CNAME 记录时将使用该域名。
5. 登录到您的 DNS 注册机构的网站，然后转至用于管理 DNS 的页面。 查找网站中标签为“域名”、“DNS”或“名称服务器管理”的链接或区域。
6. 找到您可以在其中选择或输入 A 记录和 CNAME 记录的位置。 可能需要从下拉列表中选择记录类型，或者需要转到高级设置页面。
7. 执行下列步骤以创建 A 记录：
   
   1. 选择或输入将使用 A 记录的域或子域。 例如，如果希望为 **www.customdomain.com** 创建别名，请选择“www”。 如果希望为所有子域创建通配符条目，请输入“*****”。 这将涵盖所有子域，例如 **mail.customdomain.com**、**login.customdomain.com** 和 **www.customdomain.com**。
      
       如果希望为根域创建 A 记录，它可能在注册机构的 DNS 工具中以符号“**@**”的形式列出。
   2. 在提供的字段中输入云服务的 IP 地址。 这会将 A 记录中使用的域条目与云服务部署的 IP 地址相关联。
      
       例如，以下 A 记录会将 **contoso.com** 的全部流量都转发到 **137.135.70.239**（已部署的应用程序的 IP 地址）：
      
       <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
       <tr>
       <td><strong>主机名/子域</strong></td>
       <td><strong>IP 地址</strong></td>
       </tr>
       <tr>
       <td>@</td>
       <td>137.135.70.239</td>
       </tr>
       </table>
      
       此示例展示了如何为根域创建 A 记录。 若要创建一个通配符条目来涵盖所有子域，请输入“*****”作为子域。
8. 接下来，创建一条别名为 **awverify**、规范域为前面获取的 **awverify.mysite.azurewebsites.net** 的 CNAME 记录。
   
   > [!NOTE]
   > 虽然别名 awverify 对某些注册机构而言可能有效，但是另一些注册机构可能需要完整的别名域名 awverify.www.customdomainname.com 或 awverify.customdomainname.com。
   > 
   > 
   
    例如，以下示例将创建 Azure 可以用来验证 A 记录配置的 CNAME 记录。
   
    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>别名/主机名/子域</strong></td>
    <td><strong>规范域</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.net</td>
    </tr>
    </table>

> [!NOTE]
> awverify CNAME 通过 DNS 系统向外传播可能需要一段时间。 在 awverify CNAME 传播出去之前，您无法为网站设置 A 记录定义的自定义域名。 可使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 等服务验证该 CNAME 是否可用。
> 
> 

### <a name="add-the-domain-name-to-your-website"></a>将域名添加到网站
在域名的 **awverify** CNAME 记录已传播后，必须将 A 记录定义的自定义域与网站相关联。 可以使用 Azure CLI 或 Azure 管理门户将 A 记录定义的自定义域名添加到网站。

**使用 Azure 命令行接口 (Azure CLI) 添加域名**

安装并配置 [Azure CLI](/manage/install-and-configure-cli/)，然后使用以下命令：

    azure site domain add customdomain yoursitename

例如，以下命令会将 **contoso.com** 的自定义域名添加到 **contoso.azurewebsite.net** 网站：

    azure site domain add contoso.com contoso

可以使用以下命令确认自定义域名是否已添加到网站：

    azure site domain list yoursitename

此命令返回的列表应当包含自定义域名以及默认的 **.azurewebsite.net** 条目。

**使用 Azure 管理门户添加域名**

1. 在浏览器中打开 [Azure 管理门户][portal]。
2. 在“网站”选项卡中，单击你的站点名称，选择“仪表板”，然后从页面底部选择“管理域”。
   
    ![][setcname2]
3. 在“域名”文本框中，键入已配置的域名。
   
    ![][setcname3]
4. 单击复选标记以接受该域名。

完成配置后，自定义域名将在网站的“配置”页的“域名”部分中列出。

> [!NOTE]
> 在将 A 记录定义的自定义域名添加到您的网站后，可以使用您的注册机构提供的工具删除 awverify CNAME 记录。 不过，如果您将来希望添加其他 A 记录，则必须重新创建 awverify 记录，然后才能将新的 A 记录定义的新域名与网站相关联。
> 
> 

## <a name="next-steps"></a>后续步骤
* [如何管理网站](/manage/services/web-sites/how-to-manage-websites/)
* [为网站配置 SSL 证书](/develop/net/common-tasks/enable-ssl-web-site/)

<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png


<!--HONumber=Jan17_HO3-->


