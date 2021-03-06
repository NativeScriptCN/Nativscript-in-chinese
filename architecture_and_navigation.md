# [**Architecture and Navigation**](http://docs.nativescript.org/core-concepts/navigation#architecture-and-navigation)结构和导航

NativeScript 由代表独立应用界面的页面构成。页面是 `Page` 模块的 `page` 类的实例。要在页面间导航切换，你可以使用 `Frame` 模块的 `Frame` 类的方法。

> **提示：你可以在一个单独页面里使用 \*\***[tab view](http://docs.nativescript.org/api-reference/classes/_ui_tab_view_.tabview.html)**\*\* 并在每个tab里使用不同的用户子界面来代替多页面。**

* Page management

  * Define page
  * Set home page

* Navigation

  * The topmost frame
  * Navigate by page name
  * Navigate using a function
  * Navigate and pass context
  * Navigate and set bindingContext to the page
  * Navigate without history
  * Clear history
  * Navigation transitions
  * Navigate back
  * Modal pages

* Supporting multiple screens

  * Screen size qualifiers
  * Platform qualifiers
  * Orientation qualifiers


## [**Page management**](http://docs.nativescript.org/core-concepts/navigation#page-management)页面管理

### [**Define page**](http://docs.nativescript.org/core-concepts/navigation#define-page)定义页面

页面代表你应用的不同界面。每个页面都是 `Page` 模块的 `page` 类的一个实例 。每个类实例继承了保存用户界面的根视觉元素的内容属性。nativescript提供实例化你的页面的两种方法。

**在XML里面创建页面**

您可以单独定义页面的用户界面声明和代码。

要应用这种方法，为每个页面创建一个XML文件来保持页面的布局。你的代码将位于一个JS或TS文件里。XML和JS或TS文件名称必须匹配。

### [**Example 1: Create page with XML.**](http://docs.nativescript.org/core-concepts/navigation#example-1--create-page-with-xml)用XML创建页面

> ### **XML**
> 
> ---
> 
> `<!-- main-page.xml-->`
> 
> `<Page loaded="onPageLoaded">`
> 
> `<Label text="Hello, world!"/>`
> 
> `</Page>`
> 
> ### **JS**
> 
> ---
> 
> `// main-page.js`
> 
> `function onPageLoaded(args) {`
> 
> `console.log("Page Loaded");`
> 
> `}`
> 
> `exports.onPageLoaded = onPageLoaded;`

**在代码里创建页面**

要使用这种方法，你需要创建一个叫做 `createPage` 的函数用来返回一个页面的实例。 NativeScript 把 `createPage` 作为一个生产环境函数。

### [**Example 2: Create page via code.**](http://docs.nativescript.org/core-concepts/navigation#example-2--create-page-via-code)通过代码创建页面

> ### **JS**
> 
> ---
> 
> `var pagesModule = require("ui/page");`
> 
> `var labelModule = require("ui/label");`
> 
> `function createPage() {`
> 
> `var label = new labelModule.Label();`
> 
> `label.text = "Hello, world!";`
> 
> `var page = new pagesModule.Page();`
> 
> `page.content = label; return page;`
> 
> `}`
> 
> `exports.createPage = createPage;`

### [**Set home page**](http://docs.nativescript.org/core-concepts/navigation#set-home-page)设置首页

每个应用必须有个单独的入口——首页。

要加载应用的首页，你需要用需要的 模块名称 传递 `NavigationEntry路由入口` 给 start\(\) 方法。 NativeScript 会用指定的名称寻找XML文件，解析它并按照文件的描述绘制UI。之后，如果 NativeScript 找到同名的 `JS` 或 `TS` 文件，机会执行文件里的业务逻辑。

> ### JS
> 
> ---
> 
> `var application = require("application");`
> 
> `application.start({ moduleName: "main-page" });`

## [**Navigation导航**](http://docs.nativescript.org/core-concepts/navigation#navigation)

`Frame`类代表负责在不同页面间导航的逻辑单元。通常，每个App都有一个根级别的frame—顶层frame。

要在页面间导航切换，你可以使用顶层frame实例的 `navigate` 方法。

此外，每个 `Page` 实例会在 `frame` 属性里携带导航至它的frame对象的信息。这允许你也使用frame属性来导航。

### [**顶层frame**](http://docs.nativescript.org/core-concepts/navigation#the-topmost-frame)

顶层frame是你app UI的根级容器，你可以使用它在你的app里来导航。你能通过使用 Frame 模块的 `topmost()` 方法来获得这个frame的引用。

> **JS**
> 
> ---
> 
> `var frameModule = require("ui/frame");`
> 
> `var topmost = frameModule.topmost();`

这里有进行导航的一些方法，使用哪个要基于你app的需求而定。

### [**Navigate by page name通过页面名称导航**](http://docs.nativescript.org/core-concepts/navigation#navigate-by-page-name)

可能通过指定要导航到的页面的文件名\(不带.\*，只是文件名哟\)，是导航的最简单的方法了。

> **JS**
> 
> ---
> 
> `topmost.navigate("details-page");`

### [**Navigate using a function使用函数导航**](http://docs.nativescript.org/core-concepts/navigation#navigate-using-a-function)

更加动态的导航方法可以是通过提供一个函数，该函数返回你要导航到的页面的实例。

### [**Example 3: How to navigate to a page dynamically created via code.**](http://docs.nativescript.org/core-concepts/navigation#example-3--how-to-navigate-to-a-page-dynamically-created-via-code)

> **JS**
> 
> ---
> 
> `var factoryFunc = function () {`
> 
> `var label = new labelModule.Label();`
> 
> `label.text = "Hello, world!";`
> 
> `var page = new pagesModule.Page();`
> 
> `page.content = label;`
> 
> `return page;`
> 
> `};`
> 
> `topmost.navigate(factoryFunc);`

### [**Navigate and pass context导航并传递上下文**](http://docs.nativescript.org/core-concepts/navigation#navigate-and-pass-context)

当你导航到另一个页面时，你可以用一个 `NavigationEntry` 对象来传递上下文到这个页面。对比其他导航方法而言，这个方法提供了更好的对导航的控制。例如，通过 `NavigationEntry` 你可以实现导航动画。

### [**Example 4: 如何在不同页面间传递内容.**](http://docs.nativescript.org/core-concepts/navigation#example-4--how-to-pass-content-between-different-pages)

> **JS**
> 
> ---
> 
> `var navigationEntry = {`
> 
> `moduleName: "details-page",`
> 
> `context: {info: "something you want to pass to your page"},`
> 
> `animated: false`
> 
> `};`
> 
> `topmost.navigate(navigationEntry);`

### [**导航并设置页面的绑定上下文**](http://docs.nativescript.org/core-concepts/navigation#navigate-and-set-bindingcontext-to-the-page)

当你导航时，你可以设置绑定上下文到一个页面。

### [**Example 5: 当导航到一个页面时如何自动地提供 **](http://docs.nativescript.org/core-concepts/navigation#example-5--how-to-provide-bindingcontext-automaticlly-while-navigating-to-a-page)`bindingContext`[** **](http://docs.nativescript.org/core-concepts/navigation#example-5--how-to-provide-bindingcontext-automaticlly-while-navigating-to-a-page)

> **JS**
> 
> ---
> 
> `// To import the "ui/frame" module and "main-view-model":`
> 
> `var frame = require("ui/frame");`
> 
> `var main_view_model = require("./main-view-model");`
> 
> `// Navigate to page called “my-page” and provide "bindingContext"`
> 
> `frame.topmost().navigate({`
> 
> `moduleName: "my-page",`
> 
> `bindingContext: new main_view_model.HelloWorldModel()`
> 
> `});`

#### **示例**

在下面这个例子里，这个 master-details app包括两个页面。 main 页面包含一个实物的列表。 details 页面则显示当前选择的实物的信息。

当你导航到 details 页面，你传送了被选择的实物的一个主key或ID。

### [**Example 6: 导航到详情页面并为选择项目传递内容**](http://docs.nativescript.org/core-concepts/navigation#example-6--navigate-to-the-details-page-and-pass-the-content-for-selected-item)

> **JS**
> 
> ---
> 
> `function listViewItemTap(args) {`
> 
> `// Navigate to the details page with context set to the data item for specified index`
> 
> `frames.topmost().navigate({`
> 
> `moduleName: "cuteness.io/details-page",`
> 
> `context: appViewModel.redditItems.getItem(args.index)`
> 
> `});`
> 
> `}`

通过 **onNavigatedTo** 回调函数， 你显示这个实物的信息。

### [**Example 7: 绑定从主页面收到的内容**](http://docs.nativescript.org/core-concepts/navigation#example-7--bind-the-content-received-from-main-page)

> **JS**
> 
> ---
> 
> `function pageNavigatedTo(args) {`
> 
> `var page = args.object;`
> 
> `page.bindingContext = page.navigationContext;`
> 
> `}`

### [**Navigate without history不带历史记录导航**](http://docs.nativescript.org/core-concepts/navigation#navigate-without-history)

你可以导航到一个页面时不添加这个导航到历史记录。设置 `NavigationEntry` 的 `backstackVisible` 属性为 false。如果该属性设置为false，页面会显示，但一旦从该页面导航出去则不能再导航回来了。

### [**Example 8: 页面导航，不保存导航历史记录.\_\_&gt;**](http://docs.nativescript.org/core-concepts/navigation#example-8--page-navigation-without-saving-navigation-history__)

> **JS**
> 
> ---
> 
> `var navigationEntry = {`
> 
> `moduleName: "login-page",`
> 
> `backstackVisible: false`
> 
> `};`
> 
> `topmost.navigate(navigationEntry);`

### [**Clear history清空导航历史记录**](http://docs.nativescript.org/core-concepts/navigation#clear-history)

你可以导航到一个新页面并决定完全清空全部导航历史。设置 `NavigationEntry` 的 `clearHistory` 属性为 true。这会阻止用户退回到先前访问过的页面。如果你有一个多页面身份验证过程，并且一旦用户成功登录并重定向到起始页面，你要清除身份验证页面时，这是相当有用的。

### **[Example 9: 使用](http://docs.nativescript.org/core-concepts/navigation#example-9--prevent-user-from-going-back-using-clearhistory-property)** `clearHistory`**[属性阻止用户回退](http://docs.nativescript.org/core-concepts/navigation#example-9--prevent-user-from-going-back-using-clearhistory-property)**

> **JS**
> 
> ---
> 
> `var navigationEntry = {`
> 
> `moduleName: "main-page",`
> 
> `clearHistory: true`
> 
> `};`
> 
> `topmost.navigate(navigationEntry);`

### [**Navigation transitions导航过渡**](http://docs.nativescript.org/core-concepts/navigation#navigation-transitions)

默认地，所有导航会有动画并为相应的平台使用默认的过渡 \(IOS下是UINavigationController 过渡 ，Android 下是 Fragment 过渡\) 。要改变过渡类型，设置 `NavigationEntry` 的 `navigationTransition` 属性到一个对象来符合 `NavigationTransition` 接口。

> **JS**
> 
> ---
> 
> `var navigationEntry = {`
> 
> `moduleName: "main-page",`
> 
> `animated: true,`
> 
> `transition: { name: "slide", duration: 380, curve: "easeIn" }`
> 
> `};`
> 
> `topmost.navigate(navigationEntry);`

要使用任何内建的过渡，设置 `NavigationTransition` 的 `name` 属性为下列之一：

* curl \(same as curlUp\) \(iOS only\)
* curlUp \(iOS only\)
* curlDown \(iOS only\)
* explode \(Android Lollipop and later\)
* fade
* flip \(same as flipRight\)
* flipRight
* flipLeft
* slide \(same as slideLeft\)
* slideLeft
* slideRight
* slideTop
* slideBottom

`duration`属性允许你用毫秒定义过渡的持续时间。如果不定义，会默认使用各自平台的持续时间——IOS是350毫秒，Android是300毫秒。

The `curve` property lets you specify the animation curve of the transition. Possible values are contained in the [AnimationCurve enumeration](http://docs.nativescript.org/api-reference/modules/_ui_enums_.animationcurve.html). Alternatively, you can pass an instance of type`UIViewAnimationCurve` for iOS or `android.animation.TimeInterpolator` for Android. If left undefined, and `easeInOut` curve will be used.

`curve` 属性允许你定义动画的过渡曲线。可能的值包含在 [AnimationCurve enumeration](http://docs.nativescript.org/api-reference/modules/_ui_enums_.animationcurve.html) 里。或者，你可以传递一个实例，IOS是 `UIViewAnimationCurve` 类型，Android是`android.animation.TimeInterpolator` 类型。如果不定义，会默认使用 `easeInOut` 曲线。

要指定所有frame导航的默认过渡方式，设置你要导航的frame的 `transition` 属性。

> **JS**
> 
> ---
> 
> `topmost.transition = { name: "flip" };`
> 
> `topmost.navigate("main-page");`

要指定一个默认过多到整个app的所有导航，设置 `Frame` 类的静态的 `defaultTransition属性。`

> **JS**
> 
> ---
> 
> `var frameModule = require("ui/frame");`
> 
> `frameModule.Frame.defaultTransition = { name: "fade" };`

要为不同平台指定不同的过渡，使用 `NavigationEntry` 的 `transitioniOS` 和`transitionAndroid` 属性。

### [**Example 11: 设置指定平台的过渡方式.**](http://docs.nativescript.org/core-concepts/navigation#example-11--set-up-platform-specific-transitions)

> **JS**
> 
> ---
> 
> `var navigationEntry = {`
> 
> `moduleName: "main-page",`
> 
> `animated: true,`
> 
> `transitioniOS: { name: "curl", duration: 380, curve: "easeIn" },`
> 
> `transitionAndroid: { name: "explode", duration: 300, curve: "easeOut" }`
> 
> `};`
> 
> `topmost.navigate(navigationEntry);`

### [**Custom transitions自定义过渡**](http://docs.nativescript.org/core-concepts/navigation#custom-transitions)

替代设置预置的过渡方式的 `name` 属性，你可以设置 `NavigationTransition` 的 `instance` 属性到一个继承于 `Transition` 类的实例。你可以通过编写指定平台的代码以实现动画过渡，来创建自己的用户自定义过渡。为此你需要从 `Transition` 类继承并为每个平台重写一个方法。既然这里有指定平台的代码，你就需要在两份单独的文件区别你的代码。这里有个自定义过渡的例子，它通过使用尺寸缩放过渡来放大出现的页面时缩小消失的页面。

### [**Example 12: 创建你自己的自定义过渡.**](http://docs.nativescript.org/core-concepts/navigation#example-12--create-your-own-custom-transition)

`custom-transition.android.js/ts`

> `var transition = require("ui/transition");`
> 
> `var floatType = java.lang.Float.class.getField("TYPE").get(null);`
> 
> `var CustomTransition = (function (_super) {`
> 
> `__extends(CustomTransition, _super);`
> 
> `function CustomTransition() {`
> 
> `_super.apply(this, arguments);`
> 
> `}`
> 
> `CustomTransition.prototype.createAndroidAnimator = function (transitionType) {`
> 
> `var scaleValues = java.lang.reflect.Array.newInstance(floatType, 2);`
> 
> `switch (transitionType) {`
> 
> `case transition.AndroidTransitionType.enter:`
> 
> `case transition.AndroidTransitionType.popEnter:`
> 
> `scaleValues[0] = 0;`
> 
> `scaleValues[1] = 1;`
> 
> `break;`
> 
> `case transition.AndroidTransitionType.exit:`
> 
> `case transition.AndroidTransitionType.popExit:`
> 
> `scaleValues[0] = 1;`
> 
> `scaleValues[1] = 0;`
> 
> `break;`
> 
> `}`
> 
> `var objectAnimators = java.lang.reflect.Array.newInstance(android.animation.Animator.class, 2);`
> 
> `objectAnimators[0] = android.animation.ObjectAnimator.ofFloat(null, "scaleX", scaleValues);`
> 
> `objectAnimators[1] = android.animation.ObjectAnimator.ofFloat(null, "scaleY", scaleValues);`
> 
> `var animatorSet = new android.animation.AnimatorSet();`
> 
> `animatorSet.playTogether(objectAnimators);`
> 
> `var duration = this.getDuration();`
> 
> `if (duration !== undefined) { animatorSet.setDuration(duration); }`
> 
> `animatorSet.setInterpolator(this.getCurve());`
> 
> `return animatorSet;`
> 
> `};`
> 
> `return CustomTransition;`
> 
> `})(transition.Transition);`
> 
> `exports.CustomTransition = CustomTransition;`

`custom-transition.ios.js/ts`

> `var transition = require("ui/transition");`
> 
> `var CustomTransition = (function (_super) {`
> 
> `__extends(CustomTransition, _super);`
> 
> `function CustomTransition() { _super.apply(this, arguments); }`
> 
> `CustomTransition.prototype.animateIOSTransition = function (containerView, fromView, toView, operation, completion) {`
> 
> `toView.transform = CGAffineTransformMakeScale(0, 0);`
> 
> `fromView.transform = CGAffineTransformIdentity;`
> 
> `switch (operation) {`
> 
> `case UINavigationControllerOperation.UINavigationControllerOperationPush:`
> 
> `containerView.insertSubviewAboveSubview(toView, fromView);`
> 
> `break;`
> 
> `case UINavigationControllerOperation.UINavigationControllerOperationPop:`
> 
> `containerView.insertSubviewBelowSubview(toView, fromView);`
> 
> `break;`
> 
> `}`
> 
> `var duration = this.getDuration();`
> 
> `var curve = this.getCurve();`
> 
> `UIView.animateWithDurationAnimationsCompletion(duration, function () {`
> 
> `UIView.setAnimationCurve(curve);`
> 
> `toView.transform = CGAffineTransformIdentity;`
> 
> `fromView.transform = CGAffineTransformMakeScale(0, 0);`
> 
> `}, completion);`
> 
> `};`
> 
> `return CustomTransition;`
> 
> `})(transition.Transition);`
> 
> `exports.CustomTransition = CustomTransition;`

一旦你创建了 `custom-transition.android.js/ts` 和`custom-transition.ios.js/ts` ，你就要引入该模块并实例化你的自定义 Transition ，并可选地传递一个延时和曲线给构造器。

### **[Example 13: 引入模块并实例化你的自定义过渡.](http://docs.nativescript.org/core-concepts/navigation#example-13--require-the-module-and-instantiate-your-custom-transition)**

> `var customTransition = new customTransitionModule.CustomTransition(300, "easeIn");`
> 
> `var navigationEntry = {`
> 
> `moduleName: "main-page",`
> 
> `animated: true,`
> 
> `transition: {instance: customTransition}`
> 
> `};`
> 
> `topmost.navigate(navigationEntry);`

### **[Navigate back导航返回](http://docs.nativescript.org/core-concepts/navigation#navigate-back)**

顶层frame在一个导航栈里跟踪用户访问过的页面。要返回前一个页面，你就要使用顶层frame实例的 **goBack** 方法。

> `topmost.goBack();`

### **[Modal pages模态页面](http://docs.nativescript.org/core-concepts/navigation#modal-pages)**

使用page类的 **showModal** 方法能以模态框的方式显示另一个页面。你必须指定模态页面模块的位置。你可以提供一个上下文和一个回调函数以便在模态页面关闭的时候调用。你也可以可选地指定是否以全屏的方式显示模态页面。要关闭模态页面，你需要订阅其shownmodally事件，并对通过事件参数提供的一个关闭的回调函数保存一个引用。当你准备关闭这个模态页面时调用这个函数，可选地传递一些结果到主页面。这里的例子有两个页面——一个主页面和一个登录页面。主页面模态地显示登录页面；用户输入用户名和密码并在完成后点击登录按钮。这将关闭模态登录页面并把用户名和密码返回给主页面以记录用户登进。

> ### **TIP:** 在iPhone的设计里，模态页面只以全屏方式出现。

### **[Example 14: 从模态页面接收数据.](http://docs.nativescript.org/core-concepts/navigation#example-14--receive-data-from-the-modal-page)**

**main-page**

> `var modalPageModule = "./modal-views-demo/login-page";`
> 
> `var context = "some custom context";`
> 
> `var fullscreen = true;`
> 
> `mainPage.showModal(modalPageModule, context, function closeCallback(username, password) {`
> 
> `// Log the user in...`
> 
> `}, fullscreen);`

**login-page**

> `var context;`
> 
> `var closeCallback;`
> 
> `function onShownModally(args) {`
> 
> `context = args.context;`
> 
> `closeCallback = args.closeCallback;`
> 
> `}`
> 
> `exports.onShownModally = onShownModally;`
> 
> `function onLoginButtonTap() {`
> 
> `closeCallback(usernameTextField.text, passwordTextField.text);`
> 
> `}`
> 
> `exports.onLoginButtonTap = onLoginButtonTap;`

你可以在 [这里 ](https://github.com/NativeScript/NativeScript/tree/master/apps/app/ui-tests-app/modal-view)找到完整的源代码。

## **[Supporting multiple screens支持多屏](http://docs.nativescript.org/core-concepts/navigation#supporting-multiple-screens)**

移动应用在不同设备上以不同的屏幕尺寸和形式因素运行。 NativeScript 提供了一个方式来定义不同的文件 \(.js, .css, .xml, 等等\) 以基于当前设备的屏幕尺寸、平台和方向加载。有点类似于[安卓里的多屏支持](http://developer.android.com/guide/practices/screens_support.html) 。这里有一组可以添加到文件里的适配，它\(适配\)可以在文件加载时就被重视。这是该文件看起来的样子：

`<file-name>[.<qualifier>]*.<extension>`

接下来的部分我们将通览支持的 适配 列表。

### **[Screen size qualifiers屏幕适配](http://docs.nativescript.org/core-concepts/navigation#screen-size-qualifiers)**

All the values in screen size qualifiers are in density independent pixels\(dp\) — meaning it corresponds to the physical dimensions of the screen. The assumptions is that there are ~160 dp per inch. For example, according to Android guidelines, if the device's smaller dimension is more than 600 dp \(~3.75 inches\) it is probably a tablet.

在屏幕尺寸适配的所有值都是以与密度无关的像素\(dp\)表示——意思是它对应于屏幕的物理尺寸。假设是有～160dp每英寸。例如，根据安卓的准则，如果设备较小的尺寸大于600dp\(~3.75英寸\)，它可能就是个平板。

* `minWH<X>` - 较小尺寸 \(高或宽\) 至少应是 **X** dp.
* `minW<X>` - 宽至少是 **X** dp.
* `minH<X>` - 高至少是 **X** dp.

_例如 \(独立的 XML 文件针对平板和手机\)_:

* `main-page.minWH600.xml` - XML 文件用于平板设备。
* `main-page.xml` - XML 用于手机。

### [**Platform qualifiers**](http://docs.nativescript.org/core-concepts/navigation#platform-qualifiers)**平台适配**

* `android` – Android 平台
* `ios` – iOS 平台
* `windows` \(期待中……\) – Windows 平台

_例如 \(指定平台文件\)_:

* `app.android.css` - CSS 样式表用于Android.
* `app.ios.css` - CSS 样式表用于iOS.

平台适配是在创建的时候执行，其它的是在运行的时候执行。例如， 当在为Anroid平台创建的时候，app.ios.css 文件是不会被纳入进去的。相反地，屏幕尺寸适配会在app以具体的屏幕尺寸运行在一个设备上的时候被采纳。

### [**Orientation qualifiers方向适配**](http://docs.nativescript.org/core-concepts/navigation#orientation-qualifiers)

* `land` - 风景模式的方向\(横\)。
* `port` - 肖像模式的方向\(竖\)。

> ### **注意：**
> 
> ---
> 
> **当页面加载时所有的适配都会被考虑到。然而，改变设备方向不会触发页面重新加载并不会改变页面，——也就是改变方向不会重新适配。**

