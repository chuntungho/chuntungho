---
title: 404 error in Spring MVC
date: 2021-07-30 14:40:19.33
updated: 2021-08-19 15:33:44.288
summary: How Spring MVC handle 404 errors
categories: ["Tech"]
tags: ["java", "spring", "mvc"]
---

> Tips: Source codes were extracted from Spring Boot 2.5.3.

## Scenario 1: Accessing wrong context-path

For example, the application running with context path /abc:
```properties
server.servlet.context-path = /abc
```
When you access /bcd root path, 404 error code will be returned by web container rather than Spring MVC.

![image.png](/upload/2021/07/image-46282e5e921646e8ae26eed7b1cbf0f4.png)

## Scenario 2: Accessing non-existing path

Static resouce handler (`/webjars/**`, `/**`) is added as the last item of handler mapping by default.
Those that havn't been handled by the previous handlers will eventually be handled by static resource handler.

```java
// org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport
	/**
	 * Return a handler mapping ordered at Integer.MAX_VALUE-1 with mapped
	 * resource handlers. To configure resource handling, override
	 * {@link #addResourceHandlers}.
	 */
	@Bean
	@Nullable
	public HandlerMapping resourceHandlerMapping() {
		Assert.state(this.applicationContext != null, "No ApplicationContext set");
		Assert.state(this.servletContext != null, "No ServletContext set");

		ResourceHandlerRegistry registry = new ResourceHandlerRegistry(this.applicationContext,
				this.servletContext, mvcContentNegotiationManager(), mvcUrlPathHelper());
		addResourceHandlers(registry);

		AbstractHandlerMapping handlerMapping = registry.getHandlerMapping();
		if (handlerMapping == null) {
			return null;
		}
		handlerMapping.setPathMatcher(mvcPathMatcher());
		handlerMapping.setUrlPathHelper(mvcUrlPathHelper());
		handlerMapping.setInterceptors(getInterceptors());
		handlerMapping.setCorsConfigurations(getCorsConfigurations());
		return handlerMapping;
	}

// org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration.WebMvcAutoConfigurationAdapter
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		if (!this.resourceProperties.isAddMappings()) {
			logger.debug("Default resource handling disabled");
			return;
		}
		Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
		CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
		if (!registry.hasMappingForPattern("/webjars/**")) {
			customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
					.addResourceLocations("classpath:/META-INF/resources/webjars/")
					.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
		}
		String staticPathPattern = this.mvcProperties.getStaticPathPattern();
		if (!registry.hasMappingForPattern(staticPathPattern)) {
			customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
					.addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
					.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
		}
	}
```

Since the static resource not found, 404 error code will be returned in `ResourceHttpRequestHandler`.
The error code will be resolved to view `/error/404` or `/error/4xx` by `DefaultErrorViewResolver`.

```java
// org.springframework.web.servlet.resource.ResourceHttpRequestHandler
	@Override
	public void handleRequest(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		// For very general mappings (e.g. "/") we need to check 404 first
		Resource resource = getResource(request);
		if (resource == null) {
			logger.debug("Resource not found");
			response.sendError(HttpServletResponse.SC_NOT_FOUND);
			return;
		}
		// ...
	}

// org.springframework.boot.autoconfigure.web.servlet.error.DefaultErrorViewResolver
	@Override
	public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
		ModelAndView modelAndView = resolve(String.valueOf(status.value()), model);
		if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
			modelAndView = resolve(SERIES_VIEWS.get(status.series()), model);
		}
		return modelAndView;
	}

	private ModelAndView resolve(String viewName, Map<String, Object> model) {
		String errorViewName = "error/" + viewName;
		TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
				this.applicationContext);
		if (provider != null) {
			return new ModelAndView(errorViewName, model);
		}
		return resolveResource(errorViewName, model);
	}

	private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
		for (String location : this.resourceProperties.getStaticLocations()) {
			try {
				Resource resource = this.applicationContext.getResource(location);
				resource = resource.createRelative(viewName + ".html");
				if (resource.exists()) {
					return new ModelAndView(new HtmlResourceView(resource), model);
				}
			}
			catch (Exception ex) {
			}
		}
		return null;
	}
```

If static resouce handler is disabled by following setting, 404 will be returned in `DispatcherServlet`
```properties
spring.resources.addMappings = false
```

If throwExceptionIfNoHandlerFound is enable as below.
```properties
spring.mvc.throwExceptionIfNoHandlerFound = true
```

`NoHandlerFoundException` will be thrown and can be handled by `HandlerExceptionResolver`.
```java
// org.springframework.web.servlet.DispatcherServlet
	/**
	 * No handler found -> set appropriate HTTP response status.
	 * @param request current HTTP request
	 * @param response current HTTP response
	 * @throws Exception if preparing the response failed
	 */
	protected void noHandlerFound(HttpServletRequest request, HttpServletResponse response) throws Exception {
		if (pageNotFoundLogger.isWarnEnabled()) {
			pageNotFoundLogger.warn("No mapping for " + request.getMethod() + " " + getRequestUri(request));
		}
		if (this.throwExceptionIfNoHandlerFound) {
			throw new NoHandlerFoundException(request.getMethod(), getRequestUri(request),
					new ServletServerHttpRequest(request).getHeaders());
		}
		else {
			response.sendError(HttpServletResponse.SC_NOT_FOUND);
		}
	}

	protected ModelAndView processHandlerException(HttpServletRequest request, HttpServletResponse response,
			@Nullable Object handler, Exception ex) throws Exception {

		// Success and error responses may use different content types
		request.removeAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE);

		// Check registered HandlerExceptionResolvers...
		ModelAndView exMv = null;
		if (this.handlerExceptionResolvers != null) {
			for (HandlerExceptionResolver resolver : this.handlerExceptionResolvers) {
				exMv = resolver.resolveException(request, response, handler, ex);
				if (exMv != null) {
					break;
				}
			}
		}
		//...
	}
```

## Scenario 3: Throwing 404 corresponding exception in your business code

As mentioned above, exception will be handled by `HandlerExceptionResolver`, there are some preset exception resolvers.

- `ExceptionHandlerExceptionResolver`: resolves exceptions through `@ExceptionHandler` methods
- `ResponseStatusExceptionResolver`: resolves execptions with `@ResponseStatus` annotation, or `ResponseStatusException` to HTTP status codes
- `DefaultHandlerExceptionResolver`: resolving standard Spring MVC exceptions and translating them to corresponding HTTP status codes

As response status set to 404, it will be forwarded to global error page `/error` by web container (Embbed application) or `ErrorPageFilter` (Non-embbed application, i.e. deployed WAR files).

In case of no HTTP status set to response, it will be considered as `500 - INTERNAL_SERVER_ERROR`.

```java
// org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController
	@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
	public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
		HttpStatus status = getStatus(request);
		Map<String, Object> model = Collections
				.unmodifiableMap(getErrorAttributes(request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
		response.setStatus(status.value());
		ModelAndView modelAndView = resolveErrorView(request, response, status, model);
		return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
	}

// org.springframework.boot.autoconfigure.web.servlet.error.AbstractErrorController
	protected HttpStatus getStatus(HttpServletRequest request) {
		Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
		if (statusCode == null) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
		try {
			return HttpStatus.valueOf(statusCode);
		}
		catch (Exception ex) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
	}
```

