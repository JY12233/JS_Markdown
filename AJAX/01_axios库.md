# 介绍

AJAX 是异步的 JavaScript 和 XML（Asynchronous JavaScript And XML）。简单点说，就是使用 `XMLHttpRequest` 对象与服务器通信。它可以使用 JSON，XML，HTML 和 text 文本等格式发送和接收数据。AJAX 最吸引人的就是它的“异步”特性，也就是说它可以在不重新刷新页面的情况下与服务器通信，交换数据，或更新页面。

> AJAX 是浏览器与服务器进行通信的技术。

# axios 库概览

Axios 是一个基于 Promise 的 HTTP 客户端，适用于浏览器和 Node.js 环境，具有同构特性（即同一套代码可跨平台运行）。在使用时，通常通过 `npm install axios` 进行安装，随后在代码中通过 `import axios from 'axios'` 引入，即可调用其提供的各种请求方法发起网络请求。

Axios 主要用于前后端分离架构中的数据交互、微服务通信以及第三方 API 的集成，特别适合需要统一处理请求配置、错误拦截以及复杂数据转换的现代 Web 应用开发场景。

## 核心作用与功能

Axios 的核心功能在于对原生 `XMLHttpRequest`（浏览器端）和 Node.js 原生 `http` 模块进行了高度工程化的封装。其主要作用包括：

- 自动数据转换

  自动将请求和响应的 JSON 数据进行序列化与反序列化，无需手动调用 `JSON.parse()`。

- 拦截器机制

  提供请求拦截器和响应拦截器，允许在请求发送前或响应返回后统一执行逻辑（如添加鉴权 Token、统一错误处理、显示加载状态等）。

- 请求取消

  支持通过 `CancelToken` 或 `AbortController` 取消正在进行的请求，避免不必要的网络开销。

- 并发请求控制

  内置 `axios.all` 等辅助方法，简化多个并发请求的处理流程。

- 安全性支持

  在客户端支持防御 XSRF（跨站请求伪造）攻击。

## 核心模块与类

Axios 的源码架构设计清晰，主要包含以下核心模块与类：

- `Axios` 类

  核心对象，定义了请求的入口方法（如 `request`）以及拦截器管理器，所有的 `get`、`post` 等别名方法底层均调用此类。

- `InterceptorManager`（拦截器管理器）

  负责管理请求拦截器和响应拦截器的任务队列，利用 Promise 链式调用实现中间件机制。

- `Adapters`（适配器）

  区分运行环境的适配层。在浏览器环境下使用 `xhr.js`（基于 XMLHttpRequest），在 Node.js 环境下使用 `http.js`（基于原生 http 模块）。

- `CancelToken` / `CanceledError`

  请求取消体系，通过生成信号量来中断正在进行的请求。

- `AxiosError`

  专用的错误处理类，统一了网络错误、超时错误以及服务器返回的状态码错误。

## 常用属性与配置

以下属性通常在创建 Axios 实例或发起请求时作为配置对象传入：

| 属性名称          | 作用描述                                                 | 可选值或数据类型                                             |
| ----------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| `url`             | 指定请求的服务器 URL 地址                                | 字符串（必填项）                                             |
| `method`          | 指定创建请求时使用的 HTTP 方法                           | `get`、`post`、`put`、`delete`、`patch`、`head`、`options`（默认为 `get`） |
| `baseURL`         | 自动拼接在 `url` 前面的基础路径（除非 `url` 是绝对 URL） | 字符串                                                       |
| `params`          | 指定 URL 查询参数（会自动拼接到 URL 后）                 | 普通对象或 URLSearchParams 实例                              |
| `data`            | 指定请求体内容（仅适用于 PUT、POST、PATCH）              | 字符串、普通对象、ArrayBuffer、FormData 等                   |
| `headers`         | 指定自定义的请求头信息                                   | 普通对象（如 `{ 'Authorization': 'Bearer token' }`）         |
| `timeout`         | 指定请求超时时间，超时后请求将被终止                     | 数字（单位为毫秒，默认为 `0` 即无超时）                      |
| `responseType`    | 指定服务器响应的数据类型                                 | `arraybuffer`、`blob`、`document`、`json`、`text`、`stream`（默认为 `json`） |
| `withCredentials` | 指定跨域请求时是否携带 Cookie 信息                       | 布尔值（默认为 `false`）                                     |

## 常用方法与 API

以下方法用于发起请求、创建实例或处理拦截逻辑：

| 方法名称                            | 作用描述                          | 参数说明                                                     |
| ----------------------------------- | --------------------------------- | ------------------------------------------------------------ |
| `axios.get(url[, config])`          | 发起 GET 请求，用于获取数据       | `url` 为请求地址，`config` 为可选配置对象                    |
| `axios.post(url[, data[, config]])` | 发起 POST 请求，用于提交数据      | `url` 为请求地址，`data` 为请求体，`config` 为可选配置对象   |
| `axios.put(url[, data[, config]])`  | 发起 PUT 请求，用于全量更新数据   | 参数同 `post` 方法                                           |
| `axios.delete(url[, config])`       | 发起 DELETE 请求，用于删除数据    | `url` 为请求地址，`config` 为可选配置对象                    |
| `axios.create([config])`            | 创建具有自定义配置的 Axios 新实例 | 接收一个配置对象，返回一个新的 axios 实例                    |
| `axios.all(iterable)`               | 并发执行多个请求                  | 接收一个包含多个 Promise 的可迭代对象（如数组）              |
| `axios.interceptors.request.use()`  | 添加请求拦截器                    | 接收两个回调函数：成功回调（修改 config）和失败回调（处理错误） |
| `axios.interceptors.response.use()` | 添加响应拦截器                    | 接收两个回调函数：成功回调（处理 response）和失败回调（处理 error） |

# 请求

引入 axios.js

https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js

```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

## `Get` 请求

1. 使用 axios 函数

   1. 传入配置对象。
   2. 使用 `.then` 回调函数接收结果并做后续处理。

   ```ts
   axios({
       url: "目标资源地址"
   }).then((result) => {...})
   ```

2. 查询参数

   `axios` 的参数中提供了 `params` 属性，支持以对象的形式传递。

   ```js
   axios({
       url: "目标资源地址",
       params: {
           参数名: "值"
       }
   }).then(result => {...})
   ```

   它会自动处理拼接。

## `POST` 请求

使用 `method` 和 `data` 属性。

```js
axios({
    url: "目标资源地址",
    method: "请求方法",
    data: {
        参数名: "值"
    }
}).then((result) => {...})
```

> `data` 中的参数名由后端决定。

# 错误处理

再 `then` 方法的后面，通过点语法调用 `catch` 方法，传入回调函数并定义形参。

```js
axios({...})
    .then((result) => {...})
    .catch((error) => {...})
```

# 图片上传

AJAX 图片上传是一种利用异步 JavaScript 和 XML（AJAX）技术，在不刷新当前网页的情况下，将本地图片文件传输至服务器并获取返回地址的前端交互方案。该方案的核心作用在于提升用户体验，实现图片的即时预览、头像更换、多图批量上传等功能，同时支持在上传过程中显示进度条、进行文件大小与格式校验以及处理上传错误。

## 实现原理

AJAX 图片上传的底层逻辑依赖于 HTML5 的 File API 与 `FormData` 对象。

1. 获取文件对象

   通过 `<input type="file">` 元素的 `change` 事件，从 `files` 属性中获取用户选择的本地图片文件对象（`File` 对象）。

2. 封装表单数据

   创建 `FormData` 实例，并使用 `append()` 方法将图片文件对象及附加参数（如用户 ID、分类标签）封装其中。`FormData` 能够自动处理 `multipart/form-data` 格式的编码与边界符。

3. 异步发送请求

   使用 `fetch` API 或 `axios` 库发起 POST 请求，将封装好的 `FormData` 对象作为请求体发送至服务器。

4. 处理服务器响应

   服务器接收并保存图片后，返回包含图片访问地址（URL）的 JSON 数据。前端解析响应后，将 URL 赋值给 `<img>` 标签的 `src` 属性，完成图片的显示或更新。

## 图片数据

```ts
axios
	.post("/api/photo", {
        name: file.name,
        image: file,
    })
```

这种写法会导致上传失败。因为直接将 `File` 对象放在普通 JavaScript 对象中传递给 `axios`，`axios` 会默认将其作为 JSON 数据处理。在 JSON 序列化过程中，`File` 对象会被转换为一个空对象 `{}`，导致文件内容丢失，服务器无法接收到真正的图片数据。

要成功上传图片，必须使用 `FormData` 对象来封装文件数据。`FormData` 能够自动将请求的 `Content-Type` 设置为 `multipart/form-data`，并以二进制流的形式传输文件。

```ts
// 将文件和附加参数追加到 FormData 中
formData.append('image', file); // 'image' 为服务器接收文件的字段名
formData.append('name', file.name); // 追加文件名作为附加参数
```

## 代码示例

以下分别展示基于原生 `fetch` API 与 `axios` 库实现图片上传的标准代码结构。

### fetch

```javascript
// 1. 获取文件输入框元素
const fileInput = document.getElementById('fileInput');

// 2. 监听文件选择变化事件
fileInput.addEventListener('change', async function() {
    // 3. 获取用户选择的第一个文件对象
    const file = fileInput.files[0];

    // 4. 创建 FormData 对象并追加文件
    const formData = new FormData();
    formData.append('image', file); // 'image' 为服务器接收文件的字段名

    try {
        // 5. 使用 fetch 发起 POST 请求
        const response = await fetch('/api/upload', {
            method: 'POST',
            body: formData // fetch 会自动设置正确的 Content-Type
        });

        // 6. 解析服务器返回的 JSON 数据
        const data = await response.json();

        // 7. 将返回的图片 URL 设置到 img 标签
        document.getElementById('previewImg').src = data.url;
    } catch (error) {
        console.error('图片上传失败:', error);
    }
});
```

### axios 库

```javascript
// 1. 获取文件输入框元素
const fileInput = document.getElementById('fileInput');

// 2. 监听文件选择变化事件
fileInput.addEventListener('change', function() {
    // 3. 获取用户选择的第一个文件对象
    const file = fileInput.files[0];

    // 4. 创建 FormData 对象并追加文件
    const formData = new FormData();
    formData.append('image', file); // 'image' 为服务器接收文件的字段名

    // 5. 使用 axios 发起 POST 请求
    axios.post('/api/upload', formData)
        .then(result => {
            // 6. axios 会自动解析 JSON，直接获取返回的图片 URL
            document.getElementById('previewImg').src = result.data.url;
        })
        .catch(error => {
            console.error('图片上传失败:', error);
        });
});
```

## 参数说明

| 技术或参数 | 作用描述                                                     | 可选值或数据类型                                             |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `FormData` | 用于构造键值对集合，<br />专门用于发送包含二进制文件的数据请求 | 对象（通过 `new FormData()` 创建）                           |
| `append()` | 向 `FormData` 对象中添加键值对，<br />支持添加 `File` 或 `Blob` 对象 | 方法<br />（参数：`name`，`value`，`filename`）              |
| `fetch`    | 浏览器原生提供的用于发起异步 HTTP 请求的 API                 | 函数（返回 `Promise` 对象）                                  |
| `axios`    | 基于 Promise 的第三方 HTTP 请求库，自动处理 JSON 解析        | 对象（提供 `get`、`post` 等方法）                            |
| `enctype`  | 表单数据的编码类型，<br />使用 `FormData` 时会自动设置为 `multipart/form-data` | `multipart/form-data`、<br />`application/x-www-form-urlencoded`、<br />`text/plain` |

## 注意事项

1. 前端校验

   在上传前，应通过 `File` 对象的 `type` 和 `size` 属性校验文件格式（如仅允许 `image/jpeg`、`image/png`）与大小（如限制 5MB 以内），以减少无效请求。

2. 本地预览

   可使用 `FileReader` 对象的 `readAsDataURL()` 方法，在上传前将图片转换为 Base64 字符串，实现零延迟的本地即时预览。

3. 进度条支持

   原生 `fetch` API 目前不支持上传进度监听。若需显示上传进度条，建议使用 `XMLHttpRequest` 或 `axios`（底层基于 `XMLHttpRequest`）。

4. 安全性

   服务器端必须对上传的文件进行严格校验，包括文件扩展名白名单、MIME 类型验证以及防止恶意脚本上传，同时应避免将上传目录直接暴露在 Web 可执行路径下。

# XMLHttpRequest 对象

`XMLHttpRequest`（简称 XHR）是浏览器提供的一个核心 JavaScript 对象，用于在后台与服务器进行数据通信。使用时，需先通过构造函数创建实例，随后调用 `open()` 方法初始化请求参数（如 HTTP 方法、URL 及是否异步），接着绑定状态变更或成功/失败的事件监听器，最后调用 `send()` 方法发送请求并处理返回的响应数据。该对象主要用于实现 AJAX（异步 JavaScript 和 XML）技术，在不重新加载整个页面的情况下更新网页内容、从服务器请求或接收数据，以及在后台向服务器发送数据。

## 实例化格式

```javascript
const xhr = new XMLHttpRequest(); // 创建一个新的 XMLHttpRequest 实例对象，构造函数无需传入任何参数
```

## 常用实例属性

| 属性名称             | 作用描述                                                     | 可选值或数据类型                                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `readyState`         | 表示当前请求所处的状态                                       | `0`（未初始化），<br />`1`（已建立连接），<br />`2`（已接收响应头），<br />`3`（正在接收响应体），<br />`4`（请求完成） |
| `responseText`       | 返回从服务器接收到的响应体文本内容                           | `string` 类型                                                |
| `responseXML`        | 返回从服务器接收到的响应体，解析为 XML DOM 文档对象          | `Document` 对象或 `null`                                     |
| `response`           | 返回响应体数据，具体类型由 `responseType` 属性决定           | `any` 类型                                                   |
| `responseType`       | 定义期望的服务器响应数据类型                                 | 空字符串（默认），<br />`text`，<br />`arraybuffer`，<br />`blob`，<br />`document`，<br />`json` |
| `status`             | 返回服务器响应的 HTTP 状态码                                 | `number` 类型<br />（如 `200`，`404`，`500`）                |
| `statusText`         | 返回服务器响应的 HTTP 状态文本                               | `string` 类型<br />（如 `OK`，`Not Found`）                  |
| `timeout`            | 设置请求的最大允许时间，超时后自动终止                       | `number` 类型<br />（单位：毫秒，默认 `0` 表示永不超时）     |
| `withCredentials`    | 指定跨域请求时是否携带凭证<br />（如 Cookie，HTTP 认证信息） | `true` 或 `false`（布尔值）                                  |
| `onreadystatechange` | 监听 `readyState` 属性发生变化时触发的事件处理函数           | 回调函数                                                     |
| `onload`             | 监听请求成功完成且接收到完整响应时触发的事件处理函数         | 回调函数                                                     |
| `onerror`            | 监听请求发生网络错误时触发的事件处理函数                     | 回调函数                                                     |
| `ontimeout`          | 监听请求超时触发的事件处理函数                               | 回调函数                                                     |

## 常用实例方法

| 方法名称                  | 作用描述                                                  | 参数说明                                                     |
| ------------------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| `open()`                  | 初始化一个新的 HTTP 请求参数，或重置现有请求              | `method`（`string`，HTTP 方法），<br />`url`（`string`，请求地址），<br />`async`（可选，`boolean`，是否异步），<br />`username`（可选，`string`），<br />`password`（可选，`string`） |
| `send()`                  | 实际发送 HTTP 请求到服务器                                | `body`（可选，请求体数据，支持 `string`，`Document`，`Blob`，`FormData`，`ArrayBuffer` 等类型，GET 请求通常传 `null`） |
| `setRequestHeader()`      | 设置 HTTP 请求头，必须在 `open()` 之后、`send()` 之前调用 | `name`（`string`，头部名称），`value`（`string`，头部值）    |
| `abort()`                 | 立即取消当前正在进行的请求                                | 无参数                                                       |
| `getAllResponseHeaders()` | 返回服务器响应的所有 HTTP 头部信息                        | 无参数，返回 `string` 类型                                   |
| `getResponseHeader()`     | 返回服务器响应中指定名称的 HTTP 头部值                    | `name`（`string`，要获取的头部名称），返回 `string` 或 `null` |
| `overrideMimeType()`      | 强制重写服务器返回的 MIME 类型                            | `mime`（`string`，指定的 MIME 类型）                         |

## 静态方法说明

`XMLHttpRequest` 对象没有提供自定义的静态方法。其所有核心功能均通过实例化后的对象实例来调用。在现代 Web 开发中，`XMLHttpRequest` 常作为底层实现被 `fetch` API 或第三方库（如 `axios`）封装使用。

## 代码示例

```js
// 1. 创建 XMLHttpRequest 实例
const xhr = new XMLHttpRequest();

// 2. 初始化请求（POST 方法，目标地址，异步模式）
xhr.open('POST', '/api/users', true);

// 3. 设置请求头（必须在 open() 之后、send() 之前调用）
// 告诉服务器传递的是 JSON 字符串
xhr.setRequestHeader('Content-Type', 'application/json');

// 现代开发一般使用 addEventListener -> loadend
// 4. 监听请求成功完成事件
xhr.onload = function() {
    // 判断 HTTP 状态码是否在成功范围内（200~299）
    if (xhr.status >= 200 && xhr.status < 300) {
        // 解析服务器返回的 JSON 字符串为 JavaScript 对象
        const result = JSON.parse(xhr.responseText);
        console.log('请求成功，返回数据：', result);
    } else {
        // 处理 HTTP 错误（如 404，500 等）
        console.error('请求失败，HTTP 状态码：', xhr.status);
    }
};

// 5. 监听网络层错误（如断网、跨域失败等，注意：4xx/5xx 不会触发此事件）
xhr.onerror = function() {
    console.error('发生网络错误或跨域请求被拦截');
};

// 6. 监听请求超时事件
xhr.ontimeout = function() {
    console.error('请求已超时');
};

// 7. 设置超时时间（5000 毫秒）
xhr.timeout = 5000;

// 8. 准备要发送的 JSON 数据并发送请求
const userData = { name: '张三', age: 25 };
xhr.send(JSON.stringify(userData));
```
