1 Soure Structure

* index.html  ( include a placeholder where the server-rendered)
  * <divid="root">`<!--app-html--></div>`
* server.js  #mainapplication server
* Src/
* main.tsx.              # exports env-agnostic (universal) app Code
* entry-client.tsx   # mounts the app to a DOM Element
* Entry-server.tsx # Randers the app using the framework’s SSR API

2 Setting Up the Dev Server

* server.js

  import fs from 'node:fs/promises'

  import express from 'express'

  // Constants

  const isProduction = process.env.NODE_ENV === 'production'

  const port = process.env.PORT||5173

  const base=process.env.BASE||'/'

  // Cached production assets

  const templateHtml=isProduction ? await fs.readFile('./dist/client/index.html', 'utf-8') : ''

  constssrManifest=isProduction

  ? awaitfs.readFile('./dist/client/ssr-manifest.json', 'utf-8')

  : undefined

  // Create http server

  const app = express()

  // Add Vite or respective production middlewares

  letvite

  if (!isProduction) {

  const { createServer } =await import('vite')

  vite = await createServer({

  server: { middlewareMode: true },

  appType: 'custom',

  base

  })

  app.use(vite.middlewares)

  } else {

  const compression= (await import('compression')).default

  const sirv= (await import('sirv')).default

  app.use(compression())

  app.use(base, sirv('./dist/client', { extensions: [] }))

  }

  // Serve HTML
 import fs from 'node:fs/promises'
 import express from 'express'

// Constants
const isProduction = process.env.NODE_ENV === 'production'
const port = process.env.PORT || 5173
const base = process.env.BASE || '/'

// Cached production assets
const templateHtml = isProduction
  ? await fs.readFile('./dist/client/index.html', 'utf-8')
  : ''
const ssrManifest = isProduction
  ? await fs.readFile('./dist/client/ssr-manifest.json', 'utf-8')
  : undefined

// Create http server
const app = express()

// Add Vite or respective production middlewares
let vite
if (!isProduction) {
  const { createServer } = await import('vite')
  vite = await createServer({
    server: { middlewareMode: true },
    appType: 'custom',
    base
  })
  app.use(vite.middlewares)
} else {
  const compression = (await import('compression')).default
  const sirv = (await import('sirv')).default
  app.use(compression())
  app.use(base, sirv('./dist/client', { extensions: [] }))
}

// Serve HTML
app.use('*', async (req, res) => {
  try {
    const url = req.originalUrl.replace(base, '')

    let template
    let render
    if (!isProduction) {
      // Always read fresh template in development
      template = await fs.readFile('./index.html', 'utf-8')
      template = await vite.transformIndexHtml(url, template)
      render = (await vite.ssrLoadModule('/src/entry-server.tsx')).render
    } else {
      template = templateHtml
      render = (await import('./dist/server/entry-server.js')).render
    }

    const rendered = await render(url, ssrManifest)

    const html = template
      .replace(`<!--app-head-->`, rendered.head ?? '')
      .replace(`<!--app-html-->`, rendered.html ?? '')

    res.status(200).set({ 'Content-Type': 'text/html' }).end(html)
  } catch (e) {
    vite?.ssrFixStacktrace(e)
    console.log(e.stack)
    res.status(500).end(e.stack)
  }
})

// Start http server
app.listen(port, () => {
  console.log(`Server started at http://localhost:${port}`)
})

* entry-client.tsx :

  import React from'react'

  import ReactDOMfrom 'react-dom/client'

  import App from'./app'

  ReactDOM.hydrateRoot(

  document.getElementById('root') as HTMLElement,

  <React.StrictMode>

  `<App/>`

  </React.StrictMode>)
* entry-sever.tsx :

  import React from'react'

  import ReactDOMServer from 'react-dom/client'

  import App from'./app'

  export function render () {

  const html = ReactDOMServer.rendeToString(

  <React.StrictMode>

  `<App/>`

  </React.StrictMode>) return {html} }
* package.json :

  "build:ssr": "npm run build:client && npm run build:server",

  "build:client": "vite build --ssrManifest --outDir dist/client",

  "build:server": "vite build --ssr src/entry-server.tsx --outDir dist/server"
