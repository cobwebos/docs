<properties
   pageTitle="转让 Azure 订阅 | Azure"
   description="如何将 Azure 订阅转让给另一用户，以及有关转让过程的一些常见问题 (FAQ)"
   services="billing"
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="billing"
   ms.date="04/14/2016"
   wacn.date=""/>

# 转让 Azure 订阅

你是否：

- 需要将 Azure 订阅的帐单所有权转交给其他人？
- 想要更改用于注册 Azure 的帐户？ 以前使用 Microsoft 帐户，但现在想要使用工作或学校帐户？
- 想要将 Azure 订阅从一个目录转移到另一个目录？
- 想要合并位于不同租户中的 Azure 和 Office 365？

如果你的帐户在美国，现在可以轻松地在 Microsoft Azure 帐户中心对即用即付订阅实现此目的。我们添加了相应的功能让你将订阅转让给另一用户。换言之，你现在可以更改所拥有的任何即用即付订阅的帐户管理员。

## 如何转让 Azure 订阅

1.  在 <https://account.windowsazure.cn/Subscriptions> 上登录

2.  选择要转让的订阅。

3.  单击“转让订阅”选项。

    ![Azure 帐户订阅选项卡](./media/billing-subscription-transfer/image1.png)

4.  根据提示指定接收方。

    ![“转让订阅”对话框](./media/billing-subscription-transfer/image2.PNG)

5.  接收方会自动收到含有接受链接的电子邮件。

    ![向接收方发送的订阅转让电子邮件](./media/billing-subscription-transfer/image3.png)

6.  接收方单击该链接并遵照说明操作，包括输入他们的付款信息。

    ![第一个订阅转移网页](./media/billing-subscription-transfer/image4.PNG)

    ![第二个订阅转移网页](./media/billing-subscription-transfer/image5.PNG)

7. 成功！ 订阅现已转让。

## 常见问题 (FAQ)

-   **订阅转让会造成任何服务停机吗？**

    不会影响服务。实际上这是在当前帐户管理员下取消订阅，并在接收方帐户下创建新订阅，但会将基础 Azure 服务与新订阅相关联。订阅 ID 保持不变。

-   **如何使用此机制更改订阅的目录？**-   
    Azure 订阅创建于帐户管理员所属的目录中。因此，若要更改目录，只需将订阅转让给目标目录中的用户帐户即可。当该用户完成接受转让的步骤后，订阅就会自动移至目标目录。

-   **如果我接管另一个组织的订阅帐单所有权，他们可以继续访问我的资源吗？**

    如果将订阅转让到另一个租户，与前一租户关联的用户将失去订阅的访问权限。即使用户不再是服务管理员或共同管理员，他们仍可以通过其他安全机制来访问订阅。其中包括：
    - 用于向用户授予订阅资源管理权限的管理证书。有关详细信息，请参阅[创建并上载 Azure 的管理证书](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -	存储空间等服务的访问密钥。有关详细信息，请参阅[查看、复制和重新生成存储访问密钥](/documentation/articles/storage-create-storage-account#view-copy-and-regenerate-storage-access-keys)
    -	Azure 虚拟机等服务的远程访问凭据

    此列表并不完整。如果接收方需要限制对其资源的访问，则应考虑更新与服务关联的任何机密。可按如下所述更新大多数资源：

    1.   移到 Azure 门户：[https://portal.azure.com](https://portal.azure.com)

    2.    单击“全部浏览”-&gt;“所有资源”

    3.    选择资源。这会打开资源边栏选项卡。

    4.    在资源边栏选项卡中，单击“设置”。你可以在这里查看和更新现有机密。


-   **如果我在计费周期中途转让订阅，接收方需要支付整个计费周期的费用吗？**

    发送方负责支付转让完成之前所报告的任何使用费。接收方负责支付转让后所报告的使用费。可能有些使用费是在转让之前发生的，但在转让之后才报告。这些费用将包含在接收方的帐单中。

-   **接收方是否有权访问使用与帐单历史记录？**

    目前，接收方看到的信息只有最新帐单的金额（或目前余额，如果订阅是在生成第一份帐单之前转让的话）。其余的使用与帐单历史记录不会随着订阅一起转移。

-   **转让期间可以更改优惠吗？**

    优惠肯定会保持不变。若要更改优惠，必须[联系支持人员](http://go.microsoft.com/fwlink/?LinkID=619338)。

-   **我可以将订阅转让给另一国家/地区的用户帐户吗？**

    目前不支持。接收方的用户帐户必须在相同的国家/地区。

-   **接收方可以使用不同的付款机制吗？**

    是的。这就存在以下限制：订阅帐单历史记录现已拆分到两个帐户。但好处是不需要[联系支持人员](http://go.microsoft.com/fwlink/?LinkID=619338)也能完成转让。

-   **转让 Azure 订阅后，付款方式会受到影响吗？**

    若要接受订阅转让，必须提供信用卡或类似的付款方式为订阅付款。例如，如果 Bob 将订阅转让给 Jane，而 Jane 接受转让，那么，Jane 还必须提供订阅付款方式。完成转让后，就不会再为转让给 Jane 的订阅向 Bob 收费。

## 接受订阅所有权后的后续步骤

1. 现在你是帐户管理员。请查看并更新服务管理员和共同管理员。在 [Azure 经典门户](https://manage.windowsazure.com)中转到“设置”来管理管理员。[了解详细信息](http://go.microsoft.com/fwlink/?LinkID=533293)。
2. 你还可以针对订阅和服务使用基于角色的访问控制 (RBAC)。请访问 [Azure 门户](https://portal.azure.com) [了解有关 RBAC 的详细信息](http://go.microsoft.com/fwlink/?LinkID=544802)
3. 更新与此订阅服务关联的凭据。其中包括：
    - 用于向用户授予订阅资源管理权限的管理证书。有关详细信息，请参阅[创建并上载 Azure 的管理证书](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -	存储空间等服务的访问密钥。有关详细信息，请参阅[查看、复制和重新生成存储访问密钥](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -	Azure 虚拟机等服务的远程访问凭据
4. 请在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)更新此订阅的计费警报 [了解详细信息](http://go.microsoft.com/fwlink/?LinkID=533292)
5. 	如果你正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。可以在 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)执行此操作。

<!---HONumber=Mooncake_0613_2016-->