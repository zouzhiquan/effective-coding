---
altitude: 143
author: zouzq7@163.com
created: "2017-03-03 05:05:18 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19465901380642
longitude: 117.1157518561894
source: desktop.mac
title: ajax 跨域--- cors解决方案
updated: "2017-03-03 05:37:33 +0000"
---

<div>

```  
<filter>
<filter-name>crossDomainFilter</filter-name>
<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
<init-param>
    <param-name>targetFilterLifecycle</param-name>
    <param-value>true</param-value>
</init-param>
</filter>
<filter-mapping>
<filter-name>crossDomainFilter</filter-name>
<url-pattern>/*</url-pattern><!-- 配置允许跨域访问的的url-pattern -->
</filter-mapping>
```

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>

<div>

```  
@Component("crossDomainFilter")
public class crossDomainFilter implements Filter {

    private static Logger errorLogger = LoggerFactory.getLogger(LogConstants.ERROR);

@Override
public void init(FilterConfig filterConfig) throws ServletException {
    }
    @Override
public void doFilter(ServletRequest request, ServletResponse response,
FilterChain chain) throws IOException, ServletException {
        try {
            HttpServletRequest httpRequest = (HttpServletRequest) request;
HttpServletResponse httpResponse = (HttpServletResponse) response;

// 跨域  
String origin = httpRequest.getHeader("Origin");
            if (origin == null) {
                httpResponse.addHeader("Access-Control-Allow-Origin", "*");
} else {
                httpResponse.addHeader("Access-Control-Allow-Origin", origin);
}
            httpResponse.addHeader("Access-Control-Allow-Headers", "Origin, x-requested-with, Content-Type, Accept,X-Cookie");
httpResponse.addHeader("Access-Control-Allow-Credentials", "true");
httpResponse.addHeader("Access-Control-Allow-Methods", "GET,POST,PUT,OPTIONS,DELETE");
            if ( httpRequest.getMethod().equals("OPTIONS") ) {
                httpResponse.setStatus(HttpServletResponse.SC_OK);
                return;
}
            chain.doFilter(request, response);
} catch (Exception e) {
            errorLogger.error("Exception in crossDomainFilter.doFilter", e);
            throw e;
}
    }
    @Override
public void destroy() {
    }
}  
```

</div>

<div>

[[\
] 

</div>
