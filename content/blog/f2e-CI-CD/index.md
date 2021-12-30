---
title: 前端工程化之 CI CD
date: 2021-12-30 15:18:24
---

# 前端的发展

在讲前端工程化之前，我们先了解下前端的发展史。前端的发展史可以大致分为三个阶段：前端史前记忆的**青铜时代**、进入发展期的**白银时代**和从前端到全端的**黄金时代**。

青铜时代：谷歌基于 Ajax 发 Gmail 标志着 Web 1.0（静态网页） 到 Web 2.0（动态网页） 迈进。

白银时代：最直观的表现就是前后端分离，前端和后端主要就通过 Ajax 进行交互。前端需要做的事情也更多，保存数据、处理数据、生成视图等。

黄金时代：新一代前端框架的出现 angular、React、Vue。这些框架的出现极大的提升了开发效率，也使 JS 成为前端开发的主流语言。同时也出现了前端工程化体系，持续集成、持续部署、线上监控，增强了前端开发的规范性。

前端未来的发展，随着 Web 3.0 的出现，可能会在去中心化（区块链技术）、人工智能等方向。

我记的我刚开始实习的时候，主要的工作内容就是写 HTML、CSS 和 JS，前端将写完的 HTML 文件给后端，后端使用的 PHP，然后再将 HTML 文件的动态内容进行替换。现在看起来这样的开发效率是十分低下的。

上述的发展也就是在近一二十年的过程，我认为之所以发展这么快的原因在于互联网的快速发展而出现的大量业务需求导致出现的问题，从而驱动了技术的发展。

其实最开始没有前端的概念，页面都是由后端编写，基本通过超链接进行交互。随着 Ajax 技术的出现，前端才迎来了真正的发展。

**需求**：页面不仅仅只是展示，需要满足更多交互，例如动画等（最开始都问前端是干嘛的 - 切图仔）。怎么去实现这些交互，所以出现了 JS（10天创造出来的JS），随着 JS 语言的发展，前端拥有了更大的舞台。JS 最开始分为 DOM 和 BOM ，有本书《JavaScript DOM 编程艺术》就是专门将 JS 的 DOM 操作。

**需求**：DOM 操作存在大量，重复的操作。出现了 jQuery库，对 DOM 操作进行了封装，提高了开发效率。既然 JS 都能操作 DOM，是不是就没必要去写 HTML 了？导致了模板到数据思维模式的转变，数据驱动视图（MVC、MVVM思想），从模板语言（ejs）到框架的发展。

**需求**：DOM 操作是昂贵的，JS 操作是成本小的。出现了虚拟DOM的技术方案，用 JS 对象去模拟 DOM 元素，通过对比两个对象的差异，再去执行 DOM 操作。

**需求**：随着项目的复杂性，代码量的增大就有了维护的问题。出现了模块化和工程化。

业务和技术是相辅相成的，业务驱动技术，技术反哺业务。技术最终还是需要服务于业务。

# 前端工程化

广义的前端工程化：

前端工程是软件工程的一个子类，指的是将软件工程的方法和原理运用在前端开发中, 目的是实现 **高效开发**，**有效协同**，**质量可控**。

狭义的前端工程化：

前端工程是指将 **开发阶段** 的代码转变成 **生产环境** 的代码的 一系列步骤。主要包括 **构建 , 分支管理 , 自动化测试, 部署** 等。

例子：茅草屋到高楼大厦的过程，也是一个工程化的过程。

以前造房子基本都有靠泥瓦匠几个人就可以完成，但随着需要造楼的楼越来越高，造的速度要越来越快，所以就需要借助工具和机器来完成，从而形成了工程化。

前端工程化的特点：

- 自动化
- 规范化
- 傻瓜化

# 部署系统

构建部署的工作流程

![部署流程](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1c404771-3c3e-43d3-8276-9d042d57afdb/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211230%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211230T072514Z&X-Amz-Expires=86400&X-Amz-Signature=d1e34ef17817e703d0a2f6bd2fc39a60f30a4e3d944a3474d00c1f0b3d5d5cf6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

主要环节：

1. Webhook：Webhook 是一种不同服务之间，通过订阅或推送模式来传递信息的消息通知机制。部署系统将一个 Webhook 接口注册到代码管理系统（CVS）中。开发人员提交代码后，触发 CVS 的 Webhook，由 CVS 将提交事件通知给部署系统。
2. 项目构建：部署系统在获取提交代码的消息后会创建构建任务，并推入待执行队列中，系统将依次执行任务队列中的构建任务。构建任务在执行时依次执行代码获取、依赖安装、代码构建和产物打包等环节。
3. 产物部署：构建完成后的发布代码一般分为两种模式：Push 模式和 Pull 模式。在 Push 模式下，由部署系统通过 SCP 等方式将产物包推送到目标服务器，并执行解压重启等发布流程。在 Pull 模式下会提供下载接口，由下游发布环节调用，然后获取产物包以便执行后续发布流程。同时，下游环节会调用反馈接口，将发布结果反馈至部署系统。
4. 结果反馈：构建结果与部署结果会通过通知模块（消息、邮件等）的方式，反馈至开发与测试人员。

### CI/CD

[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)（Continuous Integration，CI）和[持续交付](https://en.wikipedia.org/wiki/Continuous_delivery)（Continuous Delivery，CD），是软件生产领域提升迭代效率的一种工作方式：开发人员提交代码后由 CI/CD 系统自动化地执行合并、构建、测试和部署等一系列管道化（Pipeline）的流程，从而尽早发现和反馈代码问题，以小步快跑的方式加速软件的版本迭代过程。

这个过程通常是各系统（版本管理系统、构建系统、部署系统等）以自动化的方式协同完成的。

CI：持续集成。频繁的将代码集成到主干

CD：持续部署。代码通过测试评审后，自动部署到生产环境。

简单来说就是用工具来替代人力，开发者将更多的时间用在开发上。持续交付、敏捷开发的概念。其实目的主要为了提高开发的效率。

举个例子：以前造房子的泥瓦匠需要自己和水泥，砌砖，现在和水泥可以用机器来替代。

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d72b7107-d0e0-4b6f-9329-43a781b508ae/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211230%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211230T072534Z&X-Amz-Expires=86400&X-Amz-Signature=fd03704857c06ee251539bbff101ebb39e17061aca0598df993c21698a9f4065&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

### 部署工具

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a7c4776f-fb6f-40a3-ac22-c547a19d97b8/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211230%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211230T073159Z&X-Amz-Expires=86400&X-Amz-Signature=30e3982e484f7a8262a33ffd79051c8055e93eaea3bb46b5dccc2d4d63d63917&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

- 在这四款工具中，Jenkins 是诞生最早也最广为人知的，它的优点在于插件功能丰富且完全开源免费，因此在企业中应用较多。但缺点在于缺少特定语言环境工作流的配置模板，使用成本相对较高。此外，它的服务器需要独立部署和运维。
- CircleCI 和 Github Actions 都提供了基于容器化的云端服务的能力，提供不同的收费策略以满足普通小型开源项目和大型私有项目的各类需求。两者相比，CircleCI 胜在除了对接 Github 中的项目外，还支持 BitBucket、Heroku 等平台的流程对接。而 Github Actions 则是 Github 项目中内置的 CI/CD 工具，使用成本最低，且提供了矩阵运行、多容器测试、多工作流模板等特色功能。
- Gitlab CI 则是企业中较受欢迎的版本管理工具。Gitlab 中内置 CI/CD 工具，和 CircleCI 与 Github Actions 相同的是，Gitlab CI 也使用 yml 格式的配置文件，不同之处主要在于需要独立安装与配置 Runner。

|  | Jenkins | CircleCI | GitHub Actions | Gitlab CI |
| --- | --- | --- | --- | --- |
| 多系统 | Linux / Mac / Windows | 云服务化 | 云服务化 | Linux / Mac / Windows |
| 多语言 | 支持 | 支持 | 支持 | 支持 |
| 多环境 | 支持 | 支持 | 支持 | 支持 |
| 特性 | 插件系统 | 配置简化 | 多容器测试 | Gitlab Runner |
| 费用 | 免费 | 免费 / 收费 |  免费 / 收费 |  免费 / 收费 |

## 提高构建效率

在提高构建效率前，我们需要准备主要基于时间和基于产物的分析工具，可使用第三方工具，例如 speed-measure-webpack-plugin 和 webpack-bundle-analyzer 插件。

### 编译提效

编译提效的优化点主要有三个方向：

- 减少执行编译的模块
    - **IgnorePlugin**，构建时直接剔除需要排除的模块。例如 moment 等国际化依赖包，大多数只需要引入本国的语言包。
    - **按需引入类库模块。**这种方式一般适用于工具类库性质的依赖包的优化，例如 lodash，除了声明时指定特定模块之外，还可以使用 babel-plugin-lodash 或 babel-plugin-import 等插件。Tree Shaking 一特性也能减少产物包的体积，但有两点需要注意：1. 导入的依赖包需要使用 ES6 模块化，而 lodash 还是 Common JS，需要替换为 lodash-es 才能生效。2. 响应操作是在优化阶段进行，换句话说 Tree Shaking 并不能减少模块编译阶段的构建时间。
    - **DllPlugin**，**DllReferencePlugin**。核心思想是将项目依赖的框架等模块单独构建打包。
    - **Externals**。和  DllPlugin 解决同一类问题，主要区别：
        - 1. externals 配置更简单，DllPlugin 需要独立的配置文件。
        - 2. DllPlugin 包含了依赖包的独立构建流程，而 externals 配置中不包含依赖框架的生成方式，通常使用已传入 CDN 的依赖包。
        - 3. externals 配置的依赖包需要单独指定依赖模块的加载方式：全局对象、CommonJS、AMD 等。
        - 在引用依赖包的子模块时，DllPlugin 无须更改，而 externals 则会将子模块打入项目包中。
- 提升单个模块的构建的速度
    - **include/exclude**。include 的用途是只对符合条件的模块使用指定 Loader 进行转换处理。exclude 则正相反，不对特定条件的模块使用该 Loader。当 include 和 exclude 配置冲突时，exclude 优先级更高。
    - **noParse**。在上述 include/exclude 的基础上，进一步省略了使用默认 js 模块编译器进行编译的时间。
    - **Source Map。**
    - **TypeScript 编译优化。** 使用 ts-loader 或使用 babel-loader。其中，在使用 ts-loader 时，由于 ts-loader 默认在编译前进行类型检查，因此编译时间往往比较慢。通过加上配置项 transpileOnly: true，可以在编译时忽略类型检查，从而大大提升 TS 模块的编译速度。不过单独使用这一功能就丧失了 TS 中重要的类型检查功能，因此在许多脚手架中往往配合 ForkTsCheckerWebpackPlugin 一同使用。
    - **Resolve**。Webpack 中的 resolve 配置制定的是在构建时指定查找模块文件的规则
- 并行构建以提升总体效率
    
    并行构建的方案早在 Webpack 2 时代已经出现，随着目前最新稳定版本 Webpack 4 的发布，人们发现在一般项目的开发阶段和小型项目的各构建流程中已经用不到这种并发的思路了，因为在这些情况下，并发所需要的多进程管理与通信所带来的额外时间成本可能会超过使用工具带来的收益。但是在大中型项目的生产环境构建时，这类工具仍有发挥作用的空间。
    
    - **HappyPack** 与 **thread-loader**
    - **parallel-webpack**
    

### 打包提效

- 面向 JS 的压缩工具。
    
    Webpack 4 中内置了 [TerserWebpackPlugin](https://www.npmjs.com/package/terser-webpack-plugin) 作为默认的 JS 压缩工具，之前的版本则需要在项目配置中单独引入，早期主要使用的是 [UglifyJSWebpackPlugin](https://www.npmjs.com/package/uglifyjs-webpack-plugin)。这两个 Webpack 插件内部的压缩功能分别基于 [Terser](https://www.npmjs.com/package/terser) 和 [UglifyJS](https://github.com/mishoo/UglifyJS)。
    
- 面向 CSS 的压缩工具。
    
    CSS 同样有几种压缩工具可供选择：[OptimizeCSSAssetsPlugin](https://www.npmjs.com/package/optimize-css-assets-webpack-plugin)（在 Create-React-App 中使用）、[OptimizeCSSNanoPlugin](https://www.npmjs.com/package/@intervolga/optimize-cssnano-plugin)（在 VUE-CLI 中使用），以及[CSSMinimizerWebpackPlugin](https://www.npmjs.com/package/css-minimizer-webpack-plugin)（2020 年 Webpack 社区新发布的 CSS 压缩插件）
    
- Split Chunk 分包
- Tree Shaking
    - ES6 模块，因为依赖关系是静态分析的，而 Common JS 类型的模块是运行时。
    - 引入方式：以 default 方式引入的模块，无法被 Tree Shaking；而引入单个导出对象的方式，无论是使用 import * as xxx 的语法，还是 import {xxx} 的语法，都可以进行 Tree Shaking。
    

### 无包构建

基于浏览器的 JS 模块加载功能。主流的构建工具有 snowpack、vite。

为什么要使用构建工具？对于普通项目，要使用这种加载方案会有几个主要问题：

1. 许多其它类型的文件需要编译处理才能被浏览器正常加载（JSX、Vue、TS、CSS）。
2. 许多第三方依赖包通过第三方URL引用时，不仅过程繁琐，而且难以进行灵活的版本控制。
3. 一些辅助的开发技术，例如热更新还需要构建工具来提供。

无包构建的优点：

1. **初次构建速度快**。打包构建流程在初次启动时需要进行一系列的模块依赖分析与编译，而在无包构建流程中，这些工作都是在浏览器渲染页面时异步处理的，启动服务时只需要做少量的优化处理即可（例如缓存项目依赖的 Bare Modules），所以启动非常快。
2. **按需编译**。在打包构建流程中，启动服务时即需要完整编译打包所有模块，而无包构建流程是在浏览器渲染时，根据入口模块分析加载所需模块，编译过程按需处理，因此相比之下处理内容更少，速度也会更快。
3. **增量构建速度快**。在修改代码后的 rebuild 过程中，主流的打包构建中仍然包含编译被修改的模块和打包产物这两个主要流程，因此相比之下，只需处理编译单个模块的无包构建在速度上也会更胜一筹（尽管在打包构建工具中，也可以通过分包等方式尽可能地减少两者的差距）。

无包构建的缺点：

1. **浏览器网络请求数量剧增**。无包构建最主要面对的问题是，它的运行模式决定了在一般项目里，渲染页面所需发起的请求数远比打包构建要多得多，使得打开页面会产生瀑布式的大量网络请求，将对页面的渲染造成延迟。这对于服务稳定性和访问性能要求更高的生产环境而言，通常是不太能接受的，尤其对不支持 HTTP/2 的服务器而言，这种处理更是灾难性的。因此，一般是在开发环境下才使用无包构建，在生产环境下则仍旧使用打包构建。
2. **浏览器的兼容性**。无包构建要求浏览器支持 JavaScript module 特性，尽管目前的主流浏览器已大多支持，但是对于需要兼容旧浏览器的项目而言，仍然不可能在生产环境下使用。而在开发环境下则通常没有这种顾虑。

## 提高部署效率

### 本地部署 vs 部署系统

为什么不在开发环境直接部署代码？

前端项目的一般部署流程：

获取代码 → 安装依赖 → 源码构建 → 产物打包 → 推送代码 → 重启服务

本地部署相比部署系统的优势：

1. 在获取代码阶段，本地开发环境已经有了项目代码，不需要重新再拉取。
2. 在安装依赖阶段，本地开发环境已经构建所需的最新依赖包，即使切换到部署版本有变更，更新时间也更短。
3. 增量构建，在构建配置与项目依赖不发生变化的情况下，理论上，本地部署可以让构建进程长时间地驻留，以达到增量构建的效果。
4. 快速调试，本地部署时，构建过程会直接在本地进行，因此有任何构建问题时可以第一时间发现并处理。相比之下，远程的部署系统则需要将一定的时间消耗在链路反馈和本地环境切换上。

本地部署的缺点：

1. 流程安全风险
    1. 环境一致性。同一项目，不同开发人员的环境（系统，Nodejs 版本等）都存在差异，可能会导致打包的产物存在差异。例如 Node 项目，低版本的项目可能在高版本就运行不了。
    2. 过程一致性。尽可能地让每次部署的流程顺序、各环节的处理过程都保持一致，从而打造规范化的部署流程。本地部署依赖人工操作，会存在部署代码覆盖等情况。同时部署系统可以记录每次部署的操作日志，便于出现问题及时解决。
2. 工作效率问题
    1. 可回溯性。日志：部署过程中遇到的各种问题（构建、测试、推送代码，重启服务），遇到这些问题都需要响应的日志来帮助定位，本地部署的日志都是临时的，且最多只能保留当前一次的处理日志，如果需要对历史的日志进行分析，就不能使用这种方式。相对的，一个规范性的部署系统，则可以记录和保存每一次部署操作记录的细节日志，以及保存最近若刚次的部署代码，方便回退。
    2. 人员分工。部署代码需要消耗时间。在本地部署某一分支时，不能同时继续开发。若项目有部署权限，不仅会占用开发者的时间，还非常低效。正常情况下，部署流程是有测试人员而非研发人员发起。开发人员集中部署也会增加沟通成本。
    

### 安装提效

安装工具 npm、Yarn、Yarn with PnP、Yarn v2、pnpm

- npm：npm 是 NodeJS 自带的包管理工具，也是使用最广泛的工具之一。
- Yarn：Yarn 是 Facebook 于 2016 年发布的包管理工具，和 npm 5 之前的版本相比，Yarn 在依赖版本稳定性和安装效率方面通常更优（在 Github 中，Yarn 的 star 数量是 npm 的两倍多，可见其受欢迎程度）。
- Yarn with PnP：Yarn 自 1.12 版本开始支持 PnP 功能，旨在抛弃作为包管理目录的 node_modules，而使用软链接到本地缓存目录的方式来提升安装和模块解析的效率。
- Yarn v2：Yarn 在 2020 年初发布了 v2 版本，它和 v1 版本相比有许多重大改变，包括默认使用优化后的 PnP 等。v2 版本目前通过 Set Version 的方式安装在项目内部，而非全局安装。
- pnpm：pnpm 是于 2017 年发布的包管理工具，和 Yarn 相同，它也支持依赖版本的确定性安装特性，同时使用硬连接与符号连接缓存目录的方式，这种方式相比于非 PnP 模式下的 Yarn 安装而言磁盘存储效率更高。

依赖安装的基本流程：

1. 解析依赖关系阶段：分析项目中各依赖包的依赖关系和版本信息 
2. 下载阶段：下载依赖包
3. 链接阶段：处理项目依赖目录和缓存之间的硬链接和符号连接
- **提升依赖下载速度**
1. 依赖包下载源：各依赖管理工具默认的下载源都是位于国外的，换成国内的镜像源会对下载速度的提升十分明显。

```bash
#npm设置下载源
npm config set registry xxxx

#yarn设置下载源
yarn config set registry xxxx
```

1. 二进制下载源**：**对于一些依赖包（例如 node-sass 等），在安装过程中还需下载二进制文件，这类文件的下载不遵循 registry 的地址，因此需要对这类文件单独配置下载路径来提升下载速度。

```bash
npm config set sass-binary-site https://npm.taobao.org/mirrors/node-sass

npm config set puppeteer_download_host https://npm.taobao.org/mirrors
```

- **安装目录缓存**
1. 缓存写入：在初次安装完成后，我们可以将安装完成后的 node_modules 目录内的内容与项目的 package-lock.json 文件的内容进行关联：以 package-lock.json 文件内容的 Hash 值作为缓存的 Key，将 node_modules 目录压缩打包存储到缓存空间内。
2. 缓存读取：再次执行安装过程时，首先判断当前代码的 package-lock.json 内容的 Hash 值是否能够命中缓存目录中的 Key 值。如果命中缓存，则说明项目的依赖版本并未发生变化，因此可以直接使用缓存中的 node_modules 压缩包解压，无须再执行依赖安装的完整流程。
- **提升压缩效率的工具**

Pigz 和 Zstd

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b517bc5a-1e49-4c57-9231-51504ec7e9e0/Untitled.png)

从表格中可以发现：

- 对于同一款压缩工具来说，压缩等级越低，压缩速度越快。代价是相应的压缩率越低，压缩体积会相应增大。
- 同样使用默认压缩等级，Pigz 的压缩速度是 Gzip 的**两倍多**，而 Zstd 的压缩速度是 Gzip 的**15 倍**。
- 在都使用最高压缩等级的情况下，Pigz 的压缩速度是 Gzip 的**4 倍多**，而 Zstd 的压缩速度可以达到 Gzip 的**16 倍**。

因此，在处理大文件目录的打包压缩时，可以考虑使用这些工具来替代默认的 Gzip 压缩方式。

### 容器化方案

[容器化（Containerization）](https://en.wikipedia.org/wiki/OS-level_virtualization)通常是指以 [Docker](https://www.docker.com/) 技术为代表，将操作系统内核虚拟化的技术。和传统的虚拟机相比，容器化具有占用空间更小、性能开销更低、启动更快、支持弹性伸缩以及支持容器间互联等优势。

通常我们提到的 Docker 是指运行在 Linux/Windows/macOS 中开源的虚拟化引擎，用于创建、管理和编排容器（此外，Docker 也是发布和维护该开源项目的公司名称）。

**镜像**

Docker 中的镜像（Image）是指用于创建容器实例的基础虚拟化模板。对于开发人员来说，可以把镜像理解为编程语言中的类：通过一个镜像可以创建多个容器实例，镜像之间也存在继承关系。通过 Docker 引擎可以构建、删除镜像，还可以将本地镜像 push 到远程仓库或者从远程拉取。例如一个基于 [node:14](https://github.com/nodejs/docker-node/blob/bcd3607a38cdf5529cef9a6ed1163d7e06ddea6d/14/stretch/Dockerfile) 的镜像，在创建时不光包含了运行 node14 版本所需的 Linux 系统环境，还包含了额外打入到镜像内的 Yarn 程序。

**容器**

容器（Container）是 Docker 中的核心功能单元。通常一个容器内包含了一个或多个应用程序以及运行它们所需要的完整相关环境依赖。例如，基于上面例子中的 node:14 镜像，就可以创建出对应版本 NodeJS 的独立运行环境。通过 Docker 引擎可以对容器进行创建、删除、停止、恢复、与容器交互等操作。

**仓库**

我们可以把构建后的镜像上传到服务器，从而可以在任何地方使用到这个镜像。

### **容器化部署过程的优势**

**环境隔离**

每个项目在独立的容器内执行构建部署过程，保证容器与宿主环境之间，容器与容器之间的环境隔离，防止原先共用一台服务器时可能产生的互相影响（例如项目脚本中修改了全局配置或环境变量等）。同时，环境隔离还可以保证每个项目都可以自由定制专属的环境依赖，而不必担心对其他项目产生影响。

**多环境构建**

使用容器化部署，可以方便地针对同一个项目生成多套不同的构建环境，达到类似 Github Actions 中的矩阵构建效果，使项目可以同时检测多套环境下的集成过程。

**便于调试**

用户可通过 Xterm+SSH 的方式，通过浏览器访问部署系统中的容器环境。同传统的部署方式相比，用容器化的方式可以在部署遇到问题时让用户第一时间进入容器环境中进行现场调试，效率和便捷性大大提升。之前介绍过的部署工具 CircleCI 中就提供了这一调试功能。

**环境一致性与迁移效率**

由于部署过程的工作环境以 Docker 镜像的方式独立制作和存储，因此可以在支持 Docker 引擎的任意服务器中使用。使用时提供一致的工作环境，无须考虑不同服务器操作系统的差异。在迁移时也可以做到一键迁移，无须重复安装环境依赖。