<p align="center"><img width="128" height="128" src="./resources/logo.png" alt="logo"></p>

<p align="center">rofd </br> OFD 解析与渲染库（Rust 实现）</p>
<p align="center"></p>


# 简介

OFD（Open Form Document，版式文档）是我国自主制定的一种开放式电子文档标准。与基于版面描述的 PDF 不同，OFD 是一种基于语义的格式：文档结构与文字信息分开存储，因此比 PDF 更灵活、更易于编辑，同时支持表单填写、数字签名等更多特性。


# 项目状态

- `rofd-core`：独立的 OFD 容器解析库，支持元数据、DocID/关键词、文档书签、
  页面链接映射、页面内容、裁剪路径，以及递归解析的模板图层。
- `rofd-render`：与后端无关的显示列表，以及有界的 Cairo 渲染，支持路径、
  定位文本、PNG/JPEG 图像、裁剪、变换、旋转，以及无需整页位图分配的像素视口。
- `rofd-ffi`：面向 `rofd-core` 与 Cairo 渲染的稳定版本化 C ABI，附带独立持有的
  元数据、警告、书签和页面链接快照。
- 根 `rofd` 包：迁移期间保留的 Cairo 渲染原型。
- Qt/QML 阅读器：设计已通过评审，实现将在 C ABI 阶段之后进行。

目标架构与分阶段路线图见
[`docs/superpowers/specs/2026-09-03-rofd-library-reader-design.md`](docs/superpowers/specs/2026-09-03-rofd-library-reader-design.md)。
当前文字与图像渲染计划见
[`docs/superpowers/plans/2026-09-05-rofd-text-image-rendering.md`](docs/superpowers/plans/2026-09-05-rofd-text-image-rendering.md)。
deepin-reader 集成所需的 API 契约见
[`crates/rofd-ffi/README.md`](crates/rofd-ffi/README.md)。

# 测试可复用库

```bash
cargo fmt --all -- --check
cargo clippy -p rofd-core -p rofd-render -p rofd-ffi --all-targets -- -D warnings
cargo test -p rofd-core -p rofd-render -p rofd-ffi
cargo test -p rofd-ffi
crates/rofd-ffi/tests/run_c_tests.sh
```

`rofd-render` 需要系统安装 Cairo、FreeType/fontconfig 以完成配置好的系统字体
回退。`rofd-core` 不依赖 Cairo、图像编解码器、Qt 和 QML。各模块详细的内容支持
情况与兼容性限制见 [`rofd-core`](crates/rofd-core/README.md) 与
[`rofd-render`](crates/rofd-render/README.md) 的 README。

# 运行 Qt 原型

```bash
cargo run -p rofd --features qt-reader --bin rofd [file.ofd]
```

Qt 命令需要 `qmetaobject` 所依赖的系统 Qt6 开发包。原型通过"打开…"按钮（或
命令行传入路径）打开 OFD 文档，经由 `rofd-core` 与 `rofd-render` 渲染页面，
并支持页面翻页。注意 `-p rofd` 参数：根包不在工作区的 `default-members` 中。

# 项目结构

本项目按如下目录与文件组织：

- `crates/rofd-core/`：独立的文档解析与查询 crate。
- `crates/rofd-render/`：显示列表下沉与有界 Cairo 渲染器。
- `crates/rofd-ffi/`：稳定的 C 头文件、Rust 实现，以及独立的 C/C++ 消费方测试。
- `crates/rofd-render/tests/real_fixture.rs`：基于真实发票 fixture 的端到端
  回归测试，不依赖生成的产物。
- `src/`：迁移期间保留的旧版解析器与 Cairo 渲染器。
- `src/bin/rofd`：旧版 Qt/QML 原型。
- `src/lib.rs`：旧版库 crate。
    - `src/document.rs`：文档解析与渲染。
    - `src/page.rs`：页面解析与渲染。
    - `src/render.rs`：渲染到 Cairo surface。
    - `src/types.rs`：OFD 规范中的类型定义。
    - `src/elements.rs`：OFD 元素。
    - `src/ofd.rs`：OFD 文件解析。
- `learning/`：学习笔记与示例。
- `docs/superpowers/`：架构设计与实现计划。
- `resources/`：资源文件，如 logo。
- `LICENSE`：许可证文件。
- `Cargo.toml`：cargo 配置文件。
- `README.md`：英文自述文件。
- `README.zh_CN.md`：本自述文件。


# 参考项目

- [ofdrw](https://github.com/ofdrw/ofdrw)
- [ofd-parser](https://github.com/jyh2012/ofd-parser)
- [poppler](https://gitlab.freedesktop.org/poppler/poppler)

# 许可证

本项目基于 [GNU 宽通用公共许可证 v2.1 或更新版本](https://github.com/linuxdeepin/rofd/blob/main/LICENSE)发布。
