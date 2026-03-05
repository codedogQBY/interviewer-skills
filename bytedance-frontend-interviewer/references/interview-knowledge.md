# 前端面试技术知识点详细参考

本文档作为面试官追问技术原理时的参考知识库。按技术领域分类，包含高频考点和深挖路径。

---

## 一、JavaScript 核心原理

### 1.1 事件循环（Event Loop）

**核心机制：**
- JavaScript 是单线程执行，通过事件循环实现异步非阻塞
- 执行栈（Call Stack）→ 微任务队列（Microtask Queue）→ 宏任务队列（Macrotask Queue）
- 每次执行栈清空后，先清空所有微任务，再取一个宏任务执行

**宏任务 vs 微任务：**
- 宏任务：setTimeout、setInterval、setImmediate（Node）、I/O、UI rendering、MessageChannel
- 微任务：Promise.then/catch/finally、MutationObserver、queueMicrotask、process.nextTick（Node）

**深挖追问路径：**
- "setTimeout(fn, 0) 真的是 0ms 后执行吗？" → 最小延迟 4ms（HTML spec）
- "Promise 和 setTimeout 谁先执行？" → 微任务优先于宏任务
- "async/await 的执行时机？" → await 之后的代码相当于 .then 回调，是微任务
- "Node.js 的事件循环和浏览器有什么区别？" → 6 个阶段（timers、pending、idle、poll、check、close）

### 1.2 闭包（Closure）

**本质：** 函数 + 其引用的外部词法环境的组合

**关键点：**
- 闭包让函数可以访问其定义时的作用域，即使外部函数已执行完毕
- 变量存储在堆内存中，不会被垃圾回收（只要闭包还在引用）

**深挖追问路径：**
- "闭包会造成内存泄漏吗？" → 在 IE 旧版本中 DOM 循环引用会，现代引擎有标记清除
- "实际项目中哪里用到了闭包？" → 防抖/节流、模块模式、React Hooks
- "React Hooks 中的闭包陷阱是什么？" → 过期闭包问题，useRef 解决

### 1.3 原型与继承

**原型链：**
- 每个对象都有 `__proto__` 指向其构造函数的 `prototype`
- 属性查找沿原型链向上直到 `null`
- `Object.prototype.__proto__ === null`

**继承方式演进：**
- 原型链继承 → 构造函数继承 → 组合继承 → 寄生组合继承 → ES6 class

**深挖追问路径：**
- "class 和 function 定义的构造函数有什么区别？" → class 不会提升，必须 new 调用
- "instanceof 的原理？" → 沿原型链查找 `constructor.prototype`
- "如何实现一个 new 操作符？" → 创建对象 → 链接原型 → 执行构造函数 → 返回

### 1.4 this 指向

**规则优先级（从高到低）：**
1. new 绑定
2. 显式绑定（call/apply/bind）
3. 隐式绑定（对象方法调用）
4. 默认绑定（独立函数调用，严格模式 undefined，非严格模式 window）

**特殊情况：**
- 箭头函数没有自己的 this，继承外层函数的 this
- 事件处理函数中 this 指向触发事件的 DOM 元素
- setTimeout 中 this 指向 window（非箭头函数情况）

### 1.5 Promise

**三种状态：** pending → fulfilled / rejected（状态不可逆）

**深挖追问路径：**
- "手写一个简易 Promise" → 构造函数、then 链式调用、微任务调度
- "Promise.all 和 Promise.allSettled 的区别？" → all 有一个 reject 就 reject，allSettled 等待全部完成
- "如何实现 Promise 并发控制？" → 维护一个执行池，完成一个补充一个
- "Promise 有什么缺点？" → 无法取消，错误需要 catch 捕获

---

## 二、React 深入原理

### 2.1 Fiber 架构

**为什么需要 Fiber：**
- React 15 的 Stack Reconciler 是递归同步执行，长任务会阻塞主线程导致卡顿
- Fiber 将渲染拆分为可中断的工作单元（Unit of Work）

**Fiber 节点结构：**
- 每个 Fiber 节点包含：type、key、stateNode、child、sibling、return、pendingProps、memoizedState 等
- 形成链表结构（child → sibling → return）

**双缓存机制：**
- current Fiber 树（当前屏幕显示）和 workInProgress Fiber 树（内存构建）
- 构建完成后 root.current 指针切换（commit 阶段）

**调度机制：**
- 使用 MessageChannel（非 requestIdleCallback）实现时间切片
- 优先级调度：Immediate > UserBlocking > Normal > Low > Idle
- 可中断：每个时间切片（5ms）检查是否有更高优先级任务

**深挖追问路径：**
- "为什么不用 requestIdleCallback？" → 兼容性差，触发频率不够（一帧只触发一次）
- "React 18 的并发模式和 Fiber 的关系？" → Fiber 是并发模式的基础架构
- "什么是 Lane 模型？" → React 18 的优先级模型，用位运算表示优先级

### 2.2 Hooks 原理

**底层实现：**
- Hooks 以链表形式存储在 Fiber 节点的 memoizedState 上
- 每次渲染按调用顺序遍历链表，所以不能在条件语句中使用

**useState：**
- mountState：创建 hook 对象，初始化 state，绑定 dispatch
- updateState：从 hook.queue 取出更新链表，计算新 state

**useEffect：**
- 副作用存储在 Fiber 的 updateQueue 上
- commit 阶段异步执行（useLayoutEffect 是同步）
- 依赖数组的浅比较（Object.is）

**深挖追问路径：**
- "为什么 Hooks 不能在 if 里面用？" → 链表顺序依赖调用顺序
- "useCallback 和 useMemo 的区别？什么时候用？" → 缓存函数 vs 缓存值
- "useRef 为什么能解决闭包陷阱？" → ref.current 是可变引用，不受闭包影响

### 2.3 Virtual DOM 与 Diff

**Diff 算法三个假设：**
1. 不同类型的元素产生不同的树 → 直接替换
2. 同级元素通过 key 来标识 → 移动而非重建
3. 只做同层比较，不跨层 → O(n) 复杂度

**key 的作用：**
- 帮助 React 识别哪些元素改变了（移动/新增/删除）
- 不建议用 index 做 key：顺序变化时导致不必要的重渲染和状态错乱

### 2.4 setState 机制

- React 18 之前：在合成事件和生命周期中是"异步"的（批处理），在 setTimeout/原生事件中是同步的
- React 18：默认全部批处理（Automatic Batching）
- 本质不是异步，而是更新被放入队列，在合适的时机统一处理

---

## 三、Vue 深入原理

### 3.1 响应式系统

**Vue 2（Object.defineProperty）：**
- 遍历 data 对象，对每个属性定义 getter/setter
- getter 中收集依赖（Dep.target → Watcher）
- setter 中通知更新（dep.notify → watcher.update）
- 缺陷：无法检测属性新增/删除，数组索引修改

**Vue 3（Proxy）：**
- 使用 Proxy 代理整个对象，可以拦截 13 种操作
- 配合 Reflect 确保 this 指向正确
- 解决了 Vue 2 的所有响应式缺陷
- 引入 effect、track、trigger 三个核心函数

**深挖追问路径：**
- "Proxy 相比 defineProperty 具体好在哪？" → 支持数组、动态属性、性能更好（惰性代理）
- "Vue 3 的响应式是怎么做依赖收集的？" → WeakMap → Map → Set 的三层结构
- "computed 是怎么实现缓存的？" → dirty 标记，依赖不变时直接返回缓存值

### 3.2 模板编译

**三个阶段：**
1. **Parse**：将模板字符串解析为 AST（抽象语法树）
2. **Optimize/Transform**：标记静态节点，做静态提升（Vue 3）
3. **Generate/Codegen**：将 AST 转换为 render 函数代码

**Vue 3 编译优化：**
- 静态提升（Static Hoisting）：静态节点只创建一次
- Patch Flag：标记动态内容类型，diff 时只比较动态部分
- 缓存事件处理函数
- Block Tree：扁平化收集动态节点

### 3.3 nextTick

**实现原理：**
- 利用微任务实现异步更新队列
- 优先级：Promise.then > MutationObserver > setImmediate > setTimeout
- 将回调推入异步队列，在 DOM 更新后执行

---

## 四、前端工程化

### 4.1 Webpack

**构建流程：**
1. 初始化参数（合并配置）
2. 创建 Compiler 对象，加载所有 Plugin
3. 从 Entry 出发，调用 Loader 转换模块
4. 递归构建依赖图（Module Graph）
5. 根据依赖图生成 Chunk
6. 输出 Bundle 文件

**Loader vs Plugin：**
- Loader：文件转换器，处理单个文件（如 babel-loader 转换 JS）
- Plugin：扩展 Webpack 功能，基于 Tapable 事件系统，可以访问整个编译生命周期

**深挖追问路径：**
- "写过自定义 Loader/Plugin 吗？" → Loader 是个函数，Plugin 是包含 apply 方法的类
- "HMR 热更新的原理？" → WebSocket 推送 hash → 请求 manifest → 请求更新模块 → 模块替换
- "splitChunks 怎么配的？" → chunks/minSize/maxSize/cacheGroups

### 4.2 Vite

**为什么快：**
- 开发环境：利用浏览器原生 ESM，不需要打包，按需编译
- 使用 esbuild 做预构建（Pre-Bundling），将 CJS 转 ESM
- 生产环境使用 Rollup 打包

**和 Webpack 的核心区别：**
- Webpack：先打包再启动开发服务器（Bundle-based）
- Vite：先启动开发服务器，请求时按需编译（Native ESM-based）

### 4.3 Tree Shaking

**原理：**
- 基于 ESM 的静态分析（import/export 是静态的，编译时可确定依赖关系）
- 标记未使用的导出（Dead Code Elimination）
- 通过 `sideEffects` 字段标记无副作用的模块
- Terser/UglifyJS 在压缩阶段移除未引用代码

**深挖追问路径：**
- "为什么 CommonJS 不能 Tree Shaking？" → require 是动态的，运行时才确定依赖
- "sideEffects 字段是什么？" → 告诉打包器哪些文件没有副作用，可以安全移除

---

## 五、网络与浏览器

### 5.1 从 URL 到页面渲染

1. URL 解析
2. DNS 解析（递归查询 → 迭代查询，缓存层级：浏览器→OS→路由器→ISP）
3. TCP 三次握手（SYN → SYN+ACK → ACK）
4. TLS 握手（如果 HTTPS）
5. HTTP 请求/响应
6. 浏览器解析：HTML → DOM，CSS → CSSOM → Render Tree → Layout → Paint → Composite
7. JavaScript 执行（可能阻塞渲染）

### 5.2 HTTP 版本演进

**HTTP/1.1：**
- 持久连接（Keep-Alive）
- 管道化（Pipeline，但有队头阻塞）
- 分块传输（chunked）

**HTTP/2：**
- 多路复用（一个连接多个流）
- 头部压缩（HPACK）
- 服务器推送
- 二进制分帧

**HTTP/3：**
- 基于 QUIC（UDP）
- 解决 TCP 层的队头阻塞
- 0-RTT 连接建立
- 连接迁移（基于 Connection ID）

### 5.3 浏览器缓存

**强缓存（不请求服务器）：**
- `Cache-Control: max-age=xxx`（HTTP/1.1，优先级更高）
- `Expires: 日期`（HTTP/1.0）

**协商缓存（请求服务器验证）：**
- `Last-Modified / If-Modified-Since`（精确到秒）
- `ETag / If-None-Match`（基于内容 hash，精度更高）

**缓存位置（优先级从高到低）：**
- Service Worker
- Memory Cache
- Disk Cache
- Push Cache（HTTP/2）

### 5.4 跨域

**同源策略：** 协议 + 域名 + 端口都相同

**解决方案：**
- CORS（Cross-Origin Resource Sharing）：服务端设置 Access-Control-Allow-Origin
  - 简单请求 vs 预检请求（OPTIONS）
  - 预检请求条件：非简单方法（PUT/DELETE）或自定义头部
- JSONP：利用 `<script>` 标签不受同源策略限制
- 代理：开发环境 devServer.proxy / 生产环境 Nginx 反向代理

### 5.5 Web 安全

**XSS（跨站脚本攻击）：**
- 存储型、反射型、DOM 型
- 防范：输入过滤、输出转义、CSP（Content Security Policy）、HttpOnly Cookie

**CSRF（跨站请求伪造）：**
- 利用已认证的 Cookie 发送恶意请求
- 防范：CSRF Token、SameSite Cookie、验证 Referer/Origin

---

## 六、性能优化

### 6.1 首屏优化

- **代码分割**：路由懒加载、动态 import
- **SSR/SSG**：服务端渲染 / 静态生成
- **预加载**：preload（关键资源）、prefetch（未来资源）、dns-prefetch
- **骨架屏**：减少白屏感知时间
- **CDN**：静态资源分发
- **图片优化**：WebP、懒加载、响应式图片

### 6.2 运行时优化

- **虚拟滚动**：只渲染可视区域（如 react-virtualized）
- **防抖/节流**：控制高频操作
- **Web Worker**：计算密集型任务移到后台线程
- **RequestAnimationFrame**：动画优化
- **React.memo / useMemo / useCallback**：避免不必要重渲染

### 6.3 Web Vitals

- **LCP（Largest Contentful Paint）**：最大内容绘制，衡量加载性能，< 2.5s
- **FID（First Input Delay）**：首次输入延迟，衡量交互性，< 100ms
- **CLS（Cumulative Layout Shift）**：累计布局偏移，衡量视觉稳定性，< 0.1
- **INP（Interaction to Next Paint）**：替代 FID，衡量整体交互响应

### 6.4 内存泄漏

**常见原因：**
- 未清理的定时器和事件监听
- 闭包引用已卸载组件的状态
- 全局变量
- 分离的 DOM 引用
- 未取消的网络请求

**排查方法：**
- Chrome DevTools → Memory → Heap Snapshot
- Performance Monitor 面板观察 JS Heap Size
- 对比多次快照找到泄漏对象

---

## 七、CSS 高频考点

### 7.1 BFC（Block Formatting Context）

**触发条件：** float 不为 none、position 为 absolute/fixed、display 为 inline-block/flex/grid、overflow 不为 visible

**作用：** 清除浮动、防止 margin 重叠、自适应布局

### 7.2 Flex 布局

- `flex: 1` 等价于 `flex: 1 1 0%`（flex-grow、flex-shrink、flex-basis）
- 主轴与交叉轴
- align-items vs align-content

### 7.3 居中方案

- Flex：`display:flex; justify-content:center; align-items:center`
- Grid：`display:grid; place-items:center`
- 定位：`position:absolute; top:50%; left:50%; transform:translate(-50%,-50%)`
- 表格：`display:table-cell; vertical-align:middle; text-align:center`

---

## 八、TypeScript

### 8.1 核心概念
- 类型系统：结构化类型（鸭子类型）
- 泛型：`<T>` 参数化类型
- 联合类型 vs 交叉类型：`|` vs `&`
- type vs interface 的区别

### 8.2 高级类型
- Conditional Types：`T extends U ? X : Y`
- Mapped Types：`{ [K in keyof T]: ... }`
- Utility Types：Partial、Required、Pick、Omit、Record
- infer 关键字：在条件类型中推断类型

**深挖追问路径：**
- "type 和 interface 有什么区别？什么时候用哪个？"
- "如何实现一个 DeepPartial 类型？"
- "项目中 TS 的类型覆盖率怎么保证？any 用的多吗？"
