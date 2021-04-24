---
title: Force HTTPS redirection behind a proxy
author: Andreas Svansø Alminde
# author_title: Docusaurus Core Team
author_url: https://github.alminde.io
author_image_url: https://avatars.githubusercontent.com/u/15948257?s=400&v=4
tags: [tech, dotnet, c#]
---


This is here the stuff goes. 

```csharp title="Redirection class"
    public class ReverseProxyHttpsRedirection
    {
        private const string ForwardedProtoHeader = "X-Forwarded-Proto";
        private readonly RequestDelegate _next;

        public ReverseProxyHttpsRedirection(RequestDelegate next)
        {
            _next = next;
        }

        public async Task Invoke(HttpContext context)
        {
            var headers = context.Request.Headers;
            if (headers[ForwardedProtoHeader] == string.Empty || headers[ForwardedProtoHeader] == "https")
            {
                await _next(context);
            }
            else if (headers[ForwardedProtoHeader] != "https")
            {
                var urlWithHttps = $"https://{context.Request.Host}{context.Request.Path}{context.Request.QueryString}";
                context.Response.Redirect(urlWithHttps);
            }
        }
    }

    public static class ReverseProxyHttpsRedirectionExtensions
    {
        public static IApplicationBuilder UseReverseProxyHttpsRedirection(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<ReverseProxyHttpsRedirection>();
        }
    }
```