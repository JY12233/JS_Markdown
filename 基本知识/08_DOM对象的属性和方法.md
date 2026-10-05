# 表单

在 HTML 表单元素（如 `<input>`、`<select>`、`<textarea>` 等）中，包含 `valid` 关键字的属性和方法主要属于 **约束验证 API（Constraint Validation API）**。它们用于在客户端检查和设置表单元素的验证状态。

## 属性

1. `validity`

   - 类型

     只读属性。

   - 作用

     返回一个 `ValidityState` 对象，该对象包含了当前元素的所有验证状态信息，可以通过它来精确判断元素是因为什么原因验证失败的。

2. `ValidityState` 对象包含的常见布尔值属性：

   - `valid`

     如果元素满足所有验证规则，则为 `true`。

   - `valueMissing`

     如果元素有 `required` 属性但值为空，则为 `true`。

   - `typeMismatch`

     如果值不符合 `type` 属性（如 `email`, `url`）的格式要求，则为 `true`。

   - `patternMismatch`

     如果值不匹配 `pattern` 属性指定的正则表达式，则为 `true`。

   - `tooLong` / `tooShort`

     如果值的长度超过了 `maxlength` 或小于 `minlength`，则为 `true`。

   - `rangeOverflow` / `rangeUnderflow`

     如果数值超出了 `max` 或低于 `min` 的范围，则为 `true`。

   - `customError`

     如果通过 `setCustomValidity()` 设置了自定义错误信息，则为 `true`。

3. `validationMessage`

   - 类型

     只读属性。

   - 作用

     返回一个字符串，表示当元素验证失败时，浏览器会显示给用户的本地化错误提示信息。如果元素验证通过，则返回空字符串。

4. `willValidate`

   - 类型

     只读属性。

   - 作用

     返回一个布尔值，表示该表单元素在提交表单时是否会被验证。例如，带有 `disabled` 属性的元素或 `<button>` 类型元素的此属性为 `false`。

## 方法

- `checkValidity()`

  - 作用

    执行约束验证。如果元素的值有效，返回 `true`；如果无效，返回 `false`，并触发 `invalid` 事件。

  - 特点

    它**不会**在界面上显示浏览器默认的错误提示气泡，非常适合用于 JavaScript 中静默检查表单状态。

- `reportValidity()`

  - 作用

    同样执行约束验证并返回 `true` 或 `false`。

  - 特点

    与 `checkValidity()` 不同的是，如果验证失败，它会**主动在界面上显示**浏览器默认的错误提示气泡，就像用户点击了提交按钮一样。

- `setCustomValidity(message)`

  - 作用

    允许开发者为表单元素设置自定义的验证错误信息。

  - 用法

    - 传入一个**非空字符串**

      将元素标记为无效，并将该字符串作为错误信息。此时 `validity.customError` 变为 `true`。

    - 传入**空字符串 `""`** 

      这会清除自定义错误，将元素恢复为有效状态（前提是其他验证规则也通过了）。如果不及时清除，即使后续输入正确，表单也无法提交。

## CSS 伪类

虽然不是 JS 属性，但与验证状态紧密相关，可以通过 CSS 进行样式控制。

- **`:valid`**：选中所有通过验证的表单元素。
- **`:invalid`**：选中所有未通过验证的表单元素。

# 宽高和距离

## 元素尺寸

### offset 系列

- `element.offsetWidth` / `element.offsetHeight`

  返回元素在页面中占据的总宽度和总高度。包含内容区（content）、内边距（padding）、边框（border）以及垂直/水平滚动条的宽度/高度。不包含外边距（margin）。返回值为整数，单位为像素（px）。属于只读属性。

- `element.style.width` / `element.style.height`

  仅能获取或设置元素内联样式（inline style）中定义的宽度或高度。返回值为带单位的字符串（如 `"100px"`）。不包含 padding 和 border。属于可读可写属性。

### client 系列

- `element.clientWidth` / `element.clientHeight`

  返回元素可视内容区域的宽度和高度。包含内容区（content）和内边距（padding）。不包含边框（border）、外边距（margin）及滚动条。返回值为整数，单位为像素（px）。属于只读属性。

### scroll 系列

- `element.scrollWidth` / `element.scrollHeight`

  返回元素内容的实际总宽度和总高度。包含因溢出而被隐藏的内容区域。若内容未溢出，其值通常等于 `clientWidth` / `clientHeight`。不包含边框。返回值为整数，单位为像素（px）。属于只读属性。

### 高级计算方法

- `element.getBoundingClientRect()`

  返回一个 `DOMRect` 对象，包含元素相对于浏览器视口（viewport）的精确几何信息。其 `width` 和 `height` 属性包含了经过 CSS `transform`（如缩放）后的实际渲染尺寸，支持亚像素级别（浮点数）精度。

- `window.getComputedStyle(element)`

  返回包含元素所有最终计算后 CSS 属性值的对象。通过访问该对象的 `width` 和 `height` 属性，可获取浏览器计算后的内容区宽高（不含 padding 和 border），返回值为带单位的字符串。

## 元素位置与偏移属性

### offset 系列位置

- `element.offsetParent`

  返回距离该元素最近的已定位（即 `position` 属性不为 `static`）的祖先元素。若无已定位祖先元素，则返回 `<body>` 元素。

- `element.offsetLeft` / `element.offsetTop`

  返回当前元素的外边框（border）距离其 `offsetParent` 内边框（border）的水平/垂直距离。若父级未定位，则计算至 `<body>`。返回值为整数，单位为像素（px）。属于只读属性。

### client 系列边框

- `element.clientLeft` / `element.clientTop`

  返回元素内容区（含 padding）与元素外边框（border）左上角的间距，即元素左边框和上边框的厚度。返回值为整数，单位为像素（px）。属于只读属性。

### 视口相对位置

- **`element.getBoundingClientRect()`**

  返回的 `DOMRect` 对象中包含 `left`、`top`、`right`、`bottom` 属性，分别表示元素各边距离浏览器视口对应边缘的距离。该坐标值会随页面滚动而实时变化。

## 滚动相关属性

### 滚动偏移量

- **`element.scrollTop` / `element.scrollLeft`**

  返回元素内容向上/向左被卷去（隐藏）的距离。属于可读可写属性，可通过赋值实现程序化滚动。

### 全局页面滚动

- **`window.pageXOffset` / `window.pageYOffset`**

  返回当前文档相对于浏览器视口在水平/垂直方向上滚动的距离（等价于 `window.scrollX` / `window.scrollY`）。

## 鼠标事件坐标属性

### 视口坐标

- **`event.clientX` / `event.clientY`**

  返回鼠标指针相对于浏览器可视窗口（viewport）左上角的水平/垂直坐标。不包含页面滚动距离。

### 文档坐标

- **`event.pageX` / `event.pageY`**

  返回鼠标指针相对于整个 HTML 文档左上角的水平/垂直坐标。包含页面滚动距离。若页面无滚动条，其值与 `clientX` / `clientY` 相同。

### 元素相对坐标

- **`event.offsetX` / `event.offsetY`**

  返回鼠标指针相对于触发事件的源元素（event target）左上角的水平/垂直坐标。

### 屏幕坐标

- **`event.screenX` / `event.screenY`**：返回鼠标指针相对于用户物理屏幕左上角的水平/垂直坐标。

## 窗口与屏幕尺寸属性

- **`window.innerWidth` / `window.innerHeight`**

  返回浏览器窗口内容区域（视口）的宽度和高度，包含滚动条。

- **`window.outerWidth` / `window.outerHeight`**

  返回整个浏览器窗口的宽度和高度，包含工具栏、地址栏及窗口边框。

- **`screen.width` / `screen.height`**

  返回用户物理屏幕的总宽度和总高度。

# 样式

`element.style` 与 `getComputedStyle(element)` 区别对照表。

| 对比维度         | `element.style`                                              | `window.getComputedStyle(element)`                           |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **本质**         | 一个可读写的 `CSSStyleDeclaration` 对象，仅映射元素的内联样式。 | 一个只读的 `CSSStyleDeclaration` 对象，包含元素最终计算后的所有样式。 |
| **样式来源**     | 仅能访问和修改 HTML 标签上的 `style` 属性（内联样式）。      | 综合了内联样式、`<style>` 标签、外部 CSS 文件、浏览器默认样式以及 CSS 继承规则后的最终结果。 |
| **读写权限**     | **可读可写**。可以直接赋值来动态修改元素样式（如 `div.style.color = 'red'`）。 | **只读**。仅用于获取样式，无法通过它来修改元素样式。         |
| **返回值格式**   | 返回与 CSS 写法基本一致的字符串（如 `"rotate(30deg)"`、`"100px"`）。 | 返回浏览器计算后的绝对值或标准化格式（如 `transform` 会返回 `matrix(...)`，颜色会返回 `rgb(...)`）。 |
| **未定义样式时** | 如果内联样式中未设置该属性，返回空字符串 `""`。              | 即使未显式设置，也会返回浏览器计算后的默认值（如 `display` 默认返回 `"block"` 或 `"inline"`）。 |
| **性能开销**     | 极低。直接读取对象属性，无额外计算。                         | 较高。每次调用都会触发浏览器的样式重计算（Reflow），频繁调用可能导致性能问题。 |
| **典型应用场景** | 动态修改元素样式、读取通过 JS 动态设置的样式值。             | 获取元素的真实渲染尺寸、颜色、布局信息，用于条件判断或动画计算。 |

- **`element.style`** 是样式的“写入通道”和“内联样式读取器”。
- **`getComputedStyle(element)`** 是样式的“最终真相查询器”。

# HTMLInputElement

`HTMLInputElement` 是 DOM 中专门用于表示 `<input>` 元素的接口，它不仅继承了通用的 `HTMLElement` 属性，还封装了表单控件特有的状态管理与交互逻辑；该对象通常在通过 JavaScript 动态获取用户输入、控制表单校验状态或模拟用户交互时使用，需特别关注其 `value` 与 `defaultValue` 的区别、`validity` 校验状态对象，以及 `checkValidity()` 等约束验证方法。

## 实例化格式

在 DOM 操作中，`HTMLInputElement` 通常不通过 `new` 关键字直接实例化，而是通过 `document.createElement()` 或 DOM 查询方法获取。

```javascript
const inputElement = document.createElement('input'); // 创建一个新的 input 元素实例
inputElement.type = 'text'; // 设置输入框的类型（如 text, password, email 等）
inputElement.name = 'username'; // 设置表单提交时的字段名称
inputElement.value = '初始值'; // 设置当前输入框的值
inputElement.placeholder = '请输入用户名'; // 设置占位符提示文本
inputElement.required = true; // 设置表单提交时该字段是否为必填项
inputElement.disabled = false; // 设置输入框是否被禁用
inputElement.readOnly = false; // 设置输入框是否为只读状态
inputElement.maxLength = 50; // 设置允许输入的最大字符数
inputElement.pattern = '^[a-zA-Z0-9]+$'; // 设置用于约束验证的正则表达式
inputElement.autocomplete = 'off'; // 设置浏览器是否自动补全（on 或 off）
inputElement.multiple = true; // 允许选择多个文件
inputElement.accept = 'image/*'; // 限制文件选择器仅显示图片类型
```

## 属性说明

| 参数名         | 作用说明                                                   | 可选值类型 | 示例                                              |
| -------------- | ---------------------------------------------------------- | ---------- | ------------------------------------------------- |
| `type`         | 定义输入控件的类型，决定渲染样式与校验规则                 | 字符串     | `text`、`password`、`email`、`number`、`checkbox` |
| `name`         | 表单提交时作为键值对的键名                                 | 字符串     | `username`、`user_email`                          |
| `value`        | 获取或设置输入框当前的实际值                               | 字符串     | `admin`、`123456`                                 |
| `placeholder`  | 输入框为空时显示的简短提示文本                             | 字符串     | `请输入有效的邮箱地址`                            |
| `required`     | 布尔值，指示表单提交时是否必须填写该字段                   | 布尔值     | `true`、`false`                                   |
| `disabled`     | 布尔值，指示控件是否被禁用（禁用时不参与表单提交）         | 布尔值     | `true`、`false`                                   |
| `readOnly`     | 布尔值，指示用户是否无法修改控件的值                       | 布尔值     | `true`、`false`                                   |
| `maxLength`    | 限制文本类输入框可输入的最大字符长度                       | 整数       | `50`、`255`                                       |
| `pattern`      | 正则表达式，用于约束验证输入值的格式                       | 字符串     | `^[0-9]+$`、`[A-Za-z]{3}`                         |
| `autocomplete` | 控制浏览器是否应自动补全该字段                             | 字符串     | `on`、`off`、`new-password`                       |
| `multiple`     | 布尔值，指示是否允许选择多个值（适用于 `file` 和 `email`） | 布尔值     | `true`、`false`                                   |
| `accept`       | 提示文件选择器预期接受的文件类型（MIME 或扩展名）          | 字符串     | `image/*`、`.pdf`、`audio/*, video/*`             |

## 核心属性与常用方法

以下仅列出 `HTMLInputElement` 作为表单控件时最核心的特有属性与方法。

## 实例属性

| 属性名                            | 说明                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `value`                           | 获取或设置当前输入值，与 `defaultValue` 不同，它反映用户的实时输入 |
| `defaultValue`                    | 获取或设置 HTML 中定义的初始值，重置表单时会恢复为此值       |
| `validity`                        | 返回一个 `ValidityState` 对象，包含各项校验状态的布尔值（如 `valueMissing`、`typeMismatch`） |
| `validationMessage`               | 返回当前校验失败时浏览器显示的提示信息，校验通过则为空字符串 |
| `willValidate`                    | 布尔值，指示该元素在表单提交时是否会被校验                   |
| `form`                            | 返回该输入框所属的 `<form>` 元素引用，若不在表单内则返回 `null` |
| `checked`                         | 仅适用于 `checkbox` 或 `radio`，获取或设置选中状态           |
| `selectionStart` / `selectionEnd` | 获取或设置文本选区的起始和结束索引位置                       |
| `files`                           | 返回一个 `FileList` 对象，包含用户通过 `type="file"` 选择的文件列表；若未选择文件则为空列表 |

## 实例方法

| 方法名                          | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| `checkValidity()`               | 执行约束验证，返回布尔值；若无效则触发 `invalid` 事件，但不显示浏览器提示 |
| `reportValidity()`              | 执行约束验证，若无效则触发 `invalid` 事件并显示浏览器原生的校验提示气泡 |
| `setCustomValidity(message)`    | 设置自定义校验错误信息；传入非空字符串会使元素变为无效状态，传入空字符串则清除自定义错误 |
| `select()`                      | 选中文本输入框中的所有内容，并将焦点移至该输入框             |
| `setSelectionRange(start, end)` | 在文本输入框中选中指定范围的字符（不自动聚焦）               |
| `focus()`                       | 使输入框获得键盘焦点                                         |
| `blur()`                        | 使输入框失去键盘焦点                                         |
| `stepUp(n)` / `stepDown(n)`     | 针对 `number` 等类型，按 `step` 属性递增或递减 `n` 次值      |
| `showPicker()`                  | 以编程方式触发浏览器原生选择器（如日期、时间、颜色或文件选择器） |

# 动画

在 Web 动画开发中，JavaScript 负责逻辑控制与状态管理，而 DOM 接口则提供了与浏览器渲染引擎交互的底层能力。以下是动画开发中核心的 DOM 接口、作用及使用方法。

## `requestAnimationFrame()`

1. 定义

   浏览器提供的全局方法，用于请求浏览器在下一次重绘之前调用指定的回调函数来更新动画。

2. 作用

   - 高性能渲染

     回调函数的执行时机与浏览器的刷新率（通常为 60Hz，即每 16.6ms 一帧）严格同步，确保动画流畅。

   - 自动节流

     当页面不可见（如切换标签页）时，动画会自动暂停，节省 CPU 资源与电量。

   - 替代定时器

     完全替代传统的 `setTimeout()` 或 `setInterval()` 来实现动画循环，避免帧率抖动和丢帧。

3. 使用方法

   传入一个回调函数，该函数会接收一个高精度时间戳参数。在回调函数内部递归调用 `requestAnimationFrame()` 以形成动画循环。

```javascript
function animate(timestamp) {
    // 基于时间戳计算动画进度
    // 更新 DOM 元素的样式
    requestAnimationFrame(animate); // 递归调用，请求下一帧
}
requestAnimationFrame(animate); // 启动动画
```

## `element.animate()`

1. 定义：挂载在 DOM 元素上的方法，允许直接在 JavaScript 中定义并控制 CSS 关键帧动画。
2. 作用：
   - 声明式动画：无需在 CSS 中编写 `@keyframes`，直接在 JS 中定义关键帧和时序参数。
   - 精细控制：返回一个 `Animation` 对象，提供 `play()`、`pause()`、`reverse()`、`cancel()` 等方法，支持对动画播放状态的完全控制。
   - 时间轴管理：可动态修改 `currentTime` 或 `playbackRate`，实现快进、慢放或倒放效果。
3. 使用方法：在目标 DOM 元素上调用 `animate()`，传入关键帧数组和配置对象。

```javascript
const animation = element.animate(
    [
        { transform: 'translateX(0px)', opacity: 1 },
        { transform: 'translateX(100px)', opacity: 0 }
    ],
    {
        duration: 1000,      // 动画时长（毫秒）
        iterations: Infinity, // 无限循环
        easing: 'ease-in-out' // 缓动函数
    }
);

animation.pause(); // 暂停动画
animation.reverse(); // 反向播放
```

## CSS 动画事件接口

1. 定义

   当 CSS 动画或过渡效果的生命周期发生变化时，浏览器会在 DOM 元素上触发特定的事件。

2. 作用

   实现 JS 与 CSS 动画的联动。例如，在动画结束后执行清理逻辑、触发下一个动画或更新业务状态。

3. 核心事件

    | 事件名称             | 触发时机               | 作用说明                     |
    | -------------------- | ---------------------- | ---------------------------- |
    | `animationstart`     | CSS 动画开始播放时     | 执行动画开始前的准备工作。   |
    | `animationend`       | CSS 动画完成播放时     | 清理动画效果或触发后续动作。 |
    | `animationiteration` | CSS 动画每次循环结束时 | 在循环动画中更新状态或表现。 |
    | `transitionend`      | CSS 过渡效果完成时     | 在过渡结束后执行后续逻辑。   |

4. 使用方法：通过 `addEventListener` 监听目标元素上的对应事件。

    ```javascript
    element.addEventListener('animationend', (event) => {
        console.log('动画结束，动画名称:', event.animationName);
        element.classList.remove('is-animating');
    });
    ```

## 样式读取与布局查询

1. 定义

   用于获取元素当前计算样式的 DOM 属性与方法。

2. 作用

   在动画计算中获取元素的实时位置、尺寸或样式，用于碰撞检测、边界判断或基于当前状态的插值计算。

3. 核心接口

    | 接口名称                   | 类型     | 作用说明                                                     |
    | -------------------------- | -------- | ------------------------------------------------------------ |
    | `getComputedStyle()`       | 全局方法 | 获取元素经过 CSS 层叠计算后的最终样式值。                    |
    | `getBoundingClientRect()`  | 元素方法 | 获取元素相对于视口的位置与尺寸（包含 `top`, `left`, `width`, `height` 等）。 |
    | `offsetTop` / `offsetLeft` | 元素属性 | 获取元素相对于其偏移父元素的位置。                           |

4. 使用方法

    在动画循环或事件回调中调用，但需警惕**强制同步布局**（Forced Synchronous Layout）问题。

    ```javascript
    // 获取元素当前的计算样式
    const currentOpacity = getComputedStyle(element).opacity;
    
    // 获取元素相对于视口的位置
    const rect = element.getBoundingClientRect();
    console.log(rect.top, rect.left);
    ```

## 注意事项

1. 避免强制同步布局

   在动画循环中，严禁在修改元素样式后立即读取 `offsetTop`、`getComputedStyle()` 等属性。这会打断浏览器的“布局-绘制”流水线，导致严重的性能瓶颈。正确做法是“批量读取，批量写入”。

2. 优先使用合成属性

   在 JS 中更新动画时，优先修改 `transform` 和 `opacity` 属性。这两个属性仅触发浏览器的合成（Composite）阶段，能利用 GPU 加速，避免触发布局（Layout）与绘制（Paint），从而保证 60fps 的流畅体验。