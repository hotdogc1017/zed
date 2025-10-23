# Zed 开发：术语表

这些是在整个 zed 代码库中经常使用的一些术语和结构。

这是一个尽力而为的列表，并且正在进行中。

<!--
待办：术语表改进

问题：

- 我们可以从整个 zed 的文档注释生成此列表吗？
- 我们应该有一个部分显示各种 UI 部件及其名称。（无法在频道中执行此操作。）
-->

## 命名约定

这些通常适用于整个代码库。请注意，这里的 Name 可以是任何内容。
一个例子是 `AnyElement` 和 `LspStore`。

- `AnyName`：_name_ 的类型擦除版本。类似于 `Box<dyn NameTrait>`。
- `NameStore`：一个包装类型，抽象了操作是在本地还是远程运行。

## GPUI

### 状态管理

- `App`：一个单例，保存完整的应用程序状态，包括所有实体。关键点：`App` 不是 `Send`，这意味着 `App` 只存在于创建它的线程上（通常是主/UI 线程）。因此，如果您看到 `&mut App`，就知道您在 UI 线程上。
- `Context`：围绕 `App` 结构的包装器，具有针对特定 `Entity` 的专门行为。可以将其视为 `(&mut App, Entity<V>)`。专门的行为体现在 `Context` 的 API 表面中。例如，`App::spawn` 接受 `AsyncFnOnce(AsyncApp) -> Ret`，而 `Context::spawn` 接受 `AsyncFnOnce(WeakEntity<V>, AsyncApp) -> Ret`。
- `AsyncApp`：`App` 的拥有版本，用于异步上下文。此类型_仍然_不是 `Send`（所以 `AsyncApp` = 您在主线程上），并且任何使用它都可能是可失败的（以考虑 `App` 可能在此闭包运行时已终止的事实）。
  `AsyncApp` 的便利之处在于，您通常通过 `&mut App` 与 `App` 交互，这在异步闭包中使用会不方便；`AsyncApp` 是拥有的，因此您可以轻松地在异步闭包中使用它。
- `AppContext`：一个 trait，抽象了 `App`、`AsyncApp` 和 `Context` 及其测试版本。
- `Task`：一个在后台或前台执行器上运行或计划运行的 future。与常规 Futures 相反，Tasks 不需要 `.await` 即可开始运行。您确实需要等待它们才能获取任务的结果。
- `Executor`：用于生成在前台或后台线程上运行的任务。尝试在后台线程上运行任务。
  - `BackgroundExecutor`：运行 `Task`s 的线程池。
  - `ForegroundExecutor`：运行 `Task`s 的主线程。
- `Entity`：对由 gpui 管理的结构的强类型引用。实际上是 `App::EntityMap` 中的指针/映射键。
- `WeakEntity`：对可能不再存在的 `Entity` 的运行时检查引用。类似于 [`std::rc::Weak`](https://doc.rust-lang.org/std/rc/struct.Weak.html)。
- `Global`：一个单例类型，只有一个值，存储在 `App` 中。
- `Event`：可以由 `Entity` 发送给订阅者的数据类型
- `Action`：表示用户键盘输入的事件，可以由监听器处理
  示例：`file finder: toggle`
- `Observing`：响应实体通知它们已更改
- `Subscription`：用于响应应用程序中状态变化的事件处理程序。
  1. 发出的事件处理
  2. 观察实体的 `{new,release,on notify}`

### UI

- `View`：一个 `Entity`，可以通过其 `Render` 实现生成 `Element`。
- `Element`：可以布局并绘制到屏幕的类型。
- `element expression`：构建元素树的表达式，示例：

```rust
h_flex()
    .id(text[i])
    .relative()
    .when(selected, |this| {
        this.child(
            div()
                .h_4()
                .absolute()
                etc etc
```

- `Component`：一个构建器，可以渲染将其转换为 `Element`。
- `Dispatch tree`：待办
- `Focus`：首先处理击键的位置
- `Focus tree`：从具有当前焦点的位置到 UI 根的路径。示例 <img> 待办

## Zed UI

- `Window`：zed 中的一个结构体，表示您桌面环境中的 zed 窗口（见下图）。如果您打开了多个 zed 实例，可以有多个。主要用于渲染。
- `Modal`：浮动在其余 UI 顶部的 UI 元素
- `Picker`：表示浮动在 UI 顶部（Modal）的项目列表的结构体。您可以选择一个项目并确认。选择或确认时发生什么由选择器的委托决定。（下图中的 'Model' 是一个选择器。）
- `PickerDelegate`：用于专门化 `Picker` 行为的 trait。`Picker` 在委托字段中存储 `PickerDelegate`。
- `Center`：zed 窗口的中间部分，中心被分成多个 `Pane`。在代码库中，这是 `Workspace` 结构体上的一个字段。（见下图）。
- `Pane`：`Center` 中的一个区域，我们可以在其中放置项目，例如编辑器、多缓冲区或终端（见下图）。
- `Panel`：实现 `Panel` trait 的 `Entity`。这些可以放置在 `Dock` 中。在下图中我们看到：左侧停靠栏中的 `ProjectPanel`，底部停靠栏中的 `DebugPanel`，以及右侧停靠栏中的 `AgentPanel`。请注意 `Editor` 不实现 `Panel`，因此不是 `Panel`。
- `Dock`：类似于 `Pane` 的 UI 元素，可以打开和隐藏。一次最多可以打开 3 个停靠栏，左侧、右侧和中心下方。停靠栏包含一个或多个 `Panel`，而不是 `Pane`。（见图片）。

<img width="1921" height="1080" alt="Screenshot for the Pane and Dock features" src="https://github.com/user-attachments/assets/2cb1170e-2850-450d-89bb-73622b5d07b2" />

- `Project`：一个或多个 `Worktree`
- `Worktree`：表示本地或远程文件。

<img width="552" height="1118" alt="Screenshot for the Worktree feature" src="https://github.com/user-attachments/assets/da5c58e4-b02e-4038-9736-27e3509fdbfa" />

- [Multibuffer](https://zed.dev/docs/multibuffers)：编辑器列表，多缓冲区允许同时编辑多个文件。当 Zed 中的操作返回多个位置时，多缓冲区会打开，例如：_搜索_ 或 _转到定义_。见下图的项目搜索。

<img width="800" height="886" alt="Screenshot for the MultiBuffer feature" src="https://github.com/user-attachments/assets/d59dcecd-8ab6-4172-8fb6-b1fc3c3eaf9d" />

## Editor

- `Editor`：_那个_文本编辑器，zed 中的几乎所有内容都是一个 `Editor`，甚至是单行输入。上图中的每个窗格都包含一个或多个 `Editor` 实例。
- `Workspace`：窗口的根
- `Entry`：文件、目录、待处理目录或未加载目录。
- `Buffer`："文件"的内存表示，以及相关数据，如语法树、git 状态和诊断。
- `pending selection`：您已按下鼠标并正在拖动，但尚未释放。

## Collab

- `Collab session`：多个用户在共享的 `Project` 中工作
- `Upstream client`：已共享其工作区的 zed 客户端
- `Downstream client`：加入共享工作区的 zed 客户端

## Debugger

- `DapStore`：是一个管理调试器会话的实体
- `debugger::Session`：是一个管理调试会话生命周期和与 DAPS 通信的实体
- `BreakpointStore`：是一个管理 Zed 本地和远程实例中断点状态的实体
- `DebugSession`：管理调试会话的 UI 和运行状态
- `RunningState`：直接管理调试会话的所有视图
- `VariableList`：调试会话的变量和监视列表视图
- `Console`：待办
- `Terminal`：待办
- `BreakpointList`：待办
