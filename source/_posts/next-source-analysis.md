---
title: next源码分析
top: false
cover: false
toc: true
mathjax: true
date: 2020-07-04 11:56:15
password:
summary:
tags:
categories:
---

## 重定向路由 rewrite 参数分析过程

```js
next
  build
    //例子：
      ~/work/dev-web/next.js/examples/custom-routes-proxying/node_modules/next/dist/build/index.js
        if(typeof config.experimental.rewrites==='function'){const _rewrites=await config.experimental.rewrites();
      ~/work/dev-web/next.js/packages/next/build/index.ts
        const { headers, rewrites, redirects } = await loadCustomRoutes(config)
        ...
        routesManifest = {
          ...rewrites: rewrites.map((r) => buildCustomRoute(r, 'rewrite'))
        }
        ...
        await promises.writeFile(
          routesManifestPath, //path.join(distDir, ROUTES_MANIFEST) //routes-manifest.json
          JSON.stringify(routesManifest),
          'utf8'
        )
  next-server/server
    //例子:
      ~/work/dev-web/next.js/examples/custom-routes-proxying/node_modules/next/dist/next-server/server/next-server.js
      ~/work/dev-web/next.js/packages/next/next-server/server/next-server.ts
        ...
        this.customRoutes = this.getCustomRoutes()
        ...
            require(join(this.distDir, ROUTES_MANIFEST))
        ...
          const rewrites = this.customRoutes.rewrites.map((rewrite) => {
        ...

        this.router = new Router(this.generateRoutes())
        ...
          //generateRoutes()
          ...fsRoutes
            match: route('/_next/data/:path*'),
            ...
            await this.render(req,res,pathname,query,parsedUrl)
              ...
              this.handleRequest(req, res, parsedUrl)
                ...
                this.run(req, res, parsedUrl)
                  ...
                  const matched = await this.router.execute(req, res, parsedUrl)
                    ...
                    allRoutes= [
                      ...this.redirects
                    ]
                    for (const testRoute of allRoutes) {
```

## 服务端渲染过程

```js
generateRoutes():
  const fsRoutes: Route[] = [
    match: route('/_next/static/:path*'),
    match: route('/_next/data/:path*'),
    ...
      await this.render(req,res,pathname,...
        ...
        const html = await this.renderToHTML(req, res, pathname, query)
          const result = await this.findPageComponents(pathname, query)
            const components = await loadComponents(this.distDir,pagePath!,..
              const Component = await requirePage(pathname, distDir, serverless)
                return require(pagePath)
              const { getStaticProps, getStaticPaths, getServerSideProps } = Component
              ...
              const DocumentMod = requirePage('/_document', distDir, serverless)
              const AppMod = requirePage('/_app', distDir, serverless)
              const ComponentMod = requirePage(pathname, distDir, serverless)
              ...
              const { getServerSideProps, getStaticProps, getStaticPaths } = ComponentMod
              return {
                App,
                Document,
                Component,
                buildManifest,
                reactLoadableManifest,
                pageConfig: ComponentMod.config || {},
                getServerSideProps,
                getStaticProps,
                getStaticPaths,
              }
          ...
          await this.renderToHTMLWithComponents(req,res,pathname,result,{ ...this.renderOpts })
            // handle static page
            if (typeof components.Component === 'string') return components.Component
            ...
            const isServerProps = !!components.getServerSideProps
            ...
            const isDataReq = !!query._nextDataReq && (isSSG || isServerProps)
            ....
            const renderOpts: RenderOpts = {...components,...opts,isDataReq,}
            ...
            renderResult = await renderToHTML(req,res,pathname,query,renderOpts)
              ...
              data = await getServerSideProps({req,res,query,...})
              ...
              props.pageProps = Object.assign({}, props.pageProps, data.props)
              ...
              //renderPage
                  const {App: EnhancedApp,Component: EnhancedComponent} = enhanceComponents(options, App, Component)
                  const html = renderToString(
                      <AppContainer>
                        <EnhancedApp Component={EnhancedComponent} router={router} {...props} />
                      </AppContainer>
                    )
                  return { html, head }
              const documentCtx = { ...ctx, renderPage }
              const docProps: DocumentInitialProps = await loadGetInitialProps(Document,documentCtx)
              ...
              let html = renderDocument(Document, {props,docProps,...}
                renderToStaticMarkup(
                  <AmpStateContext.Provider value={ampState}>
                    {Document.renderDocument(Document, { __NEXT_DATA__: {gssp, // whether the page is getServerSideProps , ...docProps}
                       <DocumentComponent {...props} />
                       ...
                          render() {
                            return (
                              <Html>
                                <Head />
                                <body>
                                  <Main />
                                  <NextScript />
                                </body>
                              </Html>
                            )
                          }
                        //main
                          const { inAmpMode, html } = useContext(DocumentComponentContext)._documentProps
                          ...
                          return <div id="__next" dangerouslySetInnerHTML={{ __html: html }} />

```

## 客户端渲染过程

```js
router = createRouter(page, query, asPath, {
	subscription: ({ Component, props, err }, App) =>
      render({ App, Component, props, err }),
      	await doRender(renderingProps)
      		const appProps = { ...props, Component, err, router }
      		...
      		renderReactElement(
      		    <Root callback={resolvePromise}>
			      <AppContainer>
			        <App {...appProps} />
			      </AppContainer>
			    </Root>
			    ,document.getElementById('__next')
      		)


push(url: Url, as: Url = url, options = {})
	//pushState,replaceState 都是用来操作window.history的方法
	return this.change('pushState', url, as, options)
		routeInfo = await this.getRouteInfo(route, pathname, query, as, shallow)
			const cachedRouteInfo = this.components[route] ->
			if(shallow && cachedRouteInfo && this.route === route) return cachedRouteInfo;
			...

			routeInfo:{ Component: res.page,
			  __N_SSG: res.mod.__N_SSG,
			  __N_SSP: res.mod.__N_SSP,
			} = await this.fetchComponent(route);
				const componentResult = await this.pageLoader.loadPage(route)
					...
					const cachedPage = this.pageCache[route]
					...
					// Register a listener to get the page
      				this.pageRegisterEvents.on(route, fire)
      				...
      				this.loadingRoutes[route] = true
      				deps = await this.getDependencies(route)
      					this.loadScript(d, route)
      						script.crossOrigin = process.env.__NEXT_CROSS_ORIGIN
    						script.src = url
    						document.body.appendChild(script)
      					appendLink(d, 'stylesheet')
			...
			this._getData<RouteInfo>(() =>__N_SSG
				? this._getStaticData(dataHref!)
				: __N_SSP
            		? this._getServerData(dataHref!)
            			return fetchNextData(dataHref, this.isSsr)
            				res = await fetch(dataHref, {credentials: 'same-origin',})
            				return res.json()
            		: this.getInitialProps(Component,{pathname,query,asPath: as,} as any
              	)).then((props) => {
		          routeInfo.props = props
		          this.components[route] = routeInfo
		          return routeInfo
		        })
		this.set(route, pathname!, query, cleanedAs, routeInfo)
			data = routeInfo;
			return this.notify(data)
				return this.sub(data, this.components['/_app'].Component)
```

## next-i18n 分析

- 服务端：
  1. 服务端渲染解析出来访用户的语言，然后匹配找到语言的资源文件，
  2. 解析渲染页面所需要的命名空间，然后选择相应的向 appTree 下注入这个 i18nStore，
  3. 最后由各组件 withTranslation 选择命名空间，得到相应的字符串。
- 客户端
  next 在客户端重建 appTree 的时候，会把该 i18nStore 一同恢复。
- 缺点
  当 shallowRender router 跳转的时候，那么新页面的拿不到它所需的命名空间数据。
- 原因
  渲染 App 时顺序：1.App 的 getInitialProps 执行 2.page 的 getServerSideProps 执行 3.渲染 App 4.渲染 Component
  withAppTranslation 包装的组件会包装 App 的 getInitialProps，同时解析出此次请求需要的命名空间，但是这个时候还没有页面 getServerSideProps 的事情，所以需要在 page 页面 class 上时挂属性（例:getInitialProps）拿到响应的命名空间。
  所以客户端路由的时候，先拿 getServerProps 的数据时是带不上这个页面的 i18n 命名空间的。