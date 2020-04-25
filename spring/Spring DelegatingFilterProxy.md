# Spring DelegatingFilterProxy

---

`org.springframework.web.filter.DelegatingFilterProxy`配置，`filter-name`必须和application context中定义的bean id相同。
```
<filter>
	<filter-name>shiroFilter</filter-name>
	<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>

<filter-mapping>
	<filter-name>shiroFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>

```

DelegatingFilterProxy通过`filter-name`在`WebApplicationContext`中查找对应的`Filter`，`doFilter`动作委托给该`Filter`。
```
@Override
protected void initFilterBean() throws ServletException {
	synchronized (this.delegateMonitor) {
		if (this.delegate == null) {
			// If no target bean name specified, use filter name.
			if (this.targetBeanName == null) {
				this.targetBeanName = getFilterName();
			}
			// Fetch Spring root application context and initialize the delegate early,
			// if possible. If the root application context will be started after this
			// filter proxy, we'll have to resort to lazy initialization.
			WebApplicationContext wac = findWebApplicationContext();
			if (wac != null) {
				this.delegate = initDelegate(wac);
			}
		}
	}
}

@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain)
		throws ServletException, IOException {

	// Lazily initialize the delegate if necessary.
	Filter delegateToUse = this.delegate;
	if (delegateToUse == null) {
		synchronized (this.delegateMonitor) {
			if (this.delegate == null) {
				WebApplicationContext wac = findWebApplicationContext();
				if (wac == null) {
					throw new IllegalStateException("No WebApplicationContext found: no ContextLoaderListener registered?");
				}
				this.delegate = initDelegate(wac);
			}
			delegateToUse = this.delegate;
		}
	}

	// Let the delegate perform the actual doFilter operation.
	invokeDelegate(delegateToUse, request, response, filterChain);
}
```




