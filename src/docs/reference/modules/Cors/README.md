# CORS (`OrchardCore.Cors`)

CORS代表跨源资源共享。现代浏览器不允许从提供脚本的不同域执行脚本。这个限制被称为同源策略。为了告诉浏览器放松限制，我们可以允许在CORS模块中配置一些例外。

有关更多信息，请参见https://docs.microsoft.com/en-us/aspnet/core/security/cors和https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS。

!!! 警告
    同时使用AllowCredentials和AllowAnyOrigin被认为是一种安全风险，包含这两个选项的策略将不会被激活。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/OYXFvKWyVGo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
