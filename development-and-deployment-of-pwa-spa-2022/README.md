# Development and deployment of modern UI apps (PWAs & SPAs)

## Reading this document

Anyone with basic background in web application development can easily read and understand this document. Readers who do not have background in web application development can try google [web.dev/learn](https://web.dev/learn/) to develop a basic understanding.

## SPA

Single Page Application (via [mdn web docs](https://developer.mozilla.org/en-US/docs/Glossary/SPA))

> ```An SPA (Single-page application) is a web app implementation that loads only a single web document, and then updates the body content of that single document via JavaScript APIs such as XMLHttpRequest and Fetch when different content is to be shown.```


## Anatomy of a UI application

Modern UI Applications are becoming overly complicated with a lot of inter dependencies between different types of files. We can take a look at the diagram below and try to visualise how most of our apps look like

```mermaid
flowchart LR;
 subgraph build system
  WC[webpack.config.js] --> W[Webpack]
  T[Typescipt Compiler] --> W[Webpack]
  B[Babel Loader] --> W[Webpack]
  DL[Style loader] --> W
  CL[File Loader] --> W
 end
 subgraph source code
  A[React Component] --> B
  I[HTML] --> W[Webpack]
  C[Image] --> CL
  E[Markdown] --> W[Webpack]
  D[CSS] --> DL
  D[CSS] --> A
  C[Image] --> A
  E[Markdown] --> A
  F[Angular Component] --> T[Typescipt Compiler]
  D[CSS] --> F
  C[Image] --> F
  E[Markdown] --> F
 end
 subgraph bundle
  W --> X[Bundle]
  X[Bundle] --> Y[*.html]
  X[Bundle] --> Z["**.png|jpg|jpeg|bmp"]
  X[Bundle] --> ZA["**.[hash].min.js"]
  X[Bundle] --> ZB["**.[hash].min.css"]
 end
 subgraph SSR
  X --> WS["Web Server Express/Hapi/Webpack Dev Server"]
  WS --> WB["web Browser"]
 end
```

## Local development workflow

```mermaid
sequenceDiagram
    actor B as Web Browser
    participant N as Node.JS Server
    participant W as Webpack
    participant S as Source Code
    
    B->>N: Request index.html
    activate N
    N-->>W: Read index.html
    N-->>W: Read app.js
    activate N
    N->>N: Render app.js to string
    deactivate N
    activate N
    N->>N: Place rendered string inside index.html
    deactivate N
    N->>B: Respond with rendered index.html
    deactivate N
    
    B->>N: Request static assets linked to index.html
    activate N
    N-->>W: Read Static assets
    N->>B: Respond with static assets
    Deactivate N
    
    B->>B: Hydrate DOM
    
    loop watch filesystem
     S-->>W: *.js assets
     S-->>W: *.png|jpg|jpeg|bmp
     S-->>W: *.scss|css|less
     S-->>W: index.html
    end
```

## Build Process for Deployment

```mermaid
flowchart LR;
  subgraph Build
   S[Source Code]-->W[Webpack]
   W-->B[bundle]
   B--replace baseURI of assets with CDN baseURI-->B
  end
  subgraph deploy to cdn
   B--static assets-->CD[CDN]
  end
  subgraph deplpy to ECS/EKS
   B-->DO[Docker Image]
   DO-->DR[Docker Registry]
  end
```

## Production/Staging


```mermaid
sequenceDiagram
    actor B as Web Browser
    participant N as Node.JS Server
    participant W as Dist
    participant S as CDN
    
    B->>N: Request index.html
    activate N
    N-->>W: Read index.html
    N-->>W: Read app.js
    activate N
    N->>N: Render app.js to string
    deactivate N
    activate N
    N->>N: Place rendered string inside index.html
    deactivate N
    N->>B: Respond with rendered index.html
    deactivate N
    
    B->>N: Request static assets linked to index.html
    activate N
    N-->>W: Read Static assets
    N->>B: Respond with static assets
    Deactivate N
    
    B->>B: Hydrate DOM
    
    rect rgba(0,0,0,0.05)
     note right of B: Download static assets
     S-->>B: *.js assets
     S-->>B: *.png|jpg|jpeg|bmp
     S-->>B: *.scss|css|less
    end
```

{% include mermaid.html %}
