虽然可以将 MongoLab URI 粘贴到您的代码中，但建议在易于管理的环境中配置它。 这样，您就可以在 URI 发生更改时通过 Azure 门户更新它，而不需要使用代码。

1. 在 Azure 门户中选择“Web 应用”。
2. 在“Web Apps”列表中单击 Web 应用的名称。  
   ![WebAppEntry][entry-website]  
   将显示“Web 应用仪表板”。
3. 单击菜单栏中的“配置”。  
   ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. 向下滚动到“连接字符串”部分。  
   ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. 对于“名称”，请输入 MONGOLAB_URI。
6. 对于“值”，请粘贴在上一部分中获取的连接字符串。
7. 在“类型”下拉列表中选择“自定义”（而不是默认的“SQLAzure”）。
8. 单击工具栏上的“保存”。  
   ![SaveWebApp][button-website-save]

**注意：**Azure 会向此变量添加 **CUSTOMCONNSTR\_** 前缀，这正是上面的代码引用 **CUSTOMCONNSTR\_MONGOLAB_URI** 的原因。

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png


<!--HONumber=Jan17_HO3-->


