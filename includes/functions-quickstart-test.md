
Azure Functions 快速入门包含功能性代码，因此可以立即测试新函数。

1. 在“开发”选项卡中，查看“代码”窗口，将会发现此 Node.js 代码需要具有在消息正文或查询字符串中传递的 name 值的 HTTP 请求。 该函数运行时，此值在响应消息中返回。
   
2. 单击“测试”显示函数的内置 HTTP 测试请求窗格。
 
    ![](./media/functions-quickstart-test/function-app-develop-tab-testing.png)

3. 在“请求正文”文字框中，将 *name* 属性的值更改为你的名称，然后单击“运行”。 将看到执行由测试 HTTP 请求触发，信息写入日志且“hello...”响应在“输出”中显示。 

4. 若要从 HTTP 测试工具或其他浏览器窗口触发相同函数的执行，请从“开发”选项卡复制“函数 URL”值并粘贴到工具或浏览器地址栏中。 将查询字符串值 `&name=yourname` 追加到 URL 并执行请求。 请注意，已向日志写入相同的信息，而响应消息正文中也包含相同的字符串。

    ![](./media/functions-quickstart-test/function-app-browser-testing.png)


<!--HONumber=Feb17_HO2-->


