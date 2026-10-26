# GitHub Product Launch

**把已经能运行的软件，整理成别人看得懂、装得上、愿意试的 GitHub 项目。**

`github-product-launch` 是一个可复用的 Agent Skill：先核对产品真实能力，再准备 README、首屏 Banner、功能图、介绍视频和 Release 文案，检查完成后按用户授权发布。

它提供发布流程、素材清单和 Python 检查器。图片制作、视频渲染与 GitHub 操作由所在的 Agent 环境提供；安装 Skill 本身不会自动上传代码。

## 适合什么时候用

- 软件已经可以运行，但 GitHub 首页只有源码，别人看不出它解决什么问题。
- 准备发布一个新项目，希望一次说明清楚安装、功能、真实效果和当前限制。
- 发布新版本，希望沿用现有品牌和素材，更新介绍与下载入口。

单独修 Bug、普通 `git push`、纯 Logo 设计或与产品无关的视频，不需要启动整套发布流程。

## 最终会得到什么

| 交付物 | 说明 |
| --- | --- |
| README | 面向用户的价值说明、真实效果、核心工作流、安装、数据边界及来源许可 |
| Banner 与功能图 | 复用已有品牌，用真实界面说明使用场景；按项目需要选择数量 |
| 介绍视频 | 分镜、字幕、素材来源，以及环境具备渲染能力时导出的可播放视频和源工程 |
| 发布文案 | About、Topics、短介绍、Release 说明与下载入口 |
| `launch-manifest.json` | 将产品承诺关联到证据，将素材关联到来源和授权依据 |
| 检查报告 | 路径、素材尺寸、README 本地链接及常见敏感信息规则的检查结果 |

没有视频制作能力时，先交付分镜和素材表，并明确视频尚未生成。不会用一个动画网页冒充 MP4。

## 安装

最方便的方式是把独立仓库地址交给支持 Agent Skills 的 Agent：

> 请帮我安装这个 Skill：https://github.com/LeeYH95/github-product-launch

也可以使用命令安装：

```sh
npx skills add LeeYH95/github-product-launch --skill github-product-launch
```

`npx` 安装方式需要 Node.js。Skill 自带的检查器只依赖 **Python 3.10+ 标准库**，无需额外 Python 包。手动安装时，将整个仓库目录放入宿主支持的 Skill 目录，保留 `SKILL.md`、`references/` 和 `scripts/` 的相对位置。

无需额外初始化，不保存 GitHub 账号或密钥。远程发布复用当前环境已有的安全登录；没有登录时继续准备本地材料，再由用户完成授权。

## 怎么调用

**先准备材料，检查后再决定是否上传：**

> 用 github-product-launch 为当前项目制作一套 GitHub 发布材料。先核对真实功能，准备 README、Banner、功能图、介绍视频和检查报告。本轮只做本地材料。

**已经确定仓库，完成后发布：**

> 用 github-product-launch 整理当前项目，复用现有名称和图标，制作 GitHub 产品介绍，并发布到我指定的仓库。仓库所有者、名称和可见性如下：……

**已有仓库，只更新一个版本：**

> 用 github-product-launch 更新当前仓库的 README 和 Release 材料，重点说明这次新增的功能。复用现有素材和下载入口，不新建仓库。

最好同时告诉 Agent：产品给谁用、源码位置、运行方法、已确认的品牌、希望突出的使用场景，以及本轮是否允许远程发布。能从项目中查到的信息不需要重复填写。

## 它怎么工作

```mermaid
flowchart LR
  A[核对源码与真实功能] --> B[记录事实与素材来源]
  B --> C[制作介绍和媒体]
  C --> D[程序检查与人工审阅]
  D --> E[按授权发布并核对远程]
```

核心是让介绍、界面和代码对应。真实截图用于证明已实现的功能；生成背景可以用于装饰，不能冒充真实界面。二次开发还需要区分上游代码许可和名称、图标等品牌资产的许可。

清单填写方式、输出目录和失败恢复见 [使用示例](docs/USAGE.md)。面向 Agent 的具体流程在 [SKILL.md](SKILL.md)。

## 单独运行检查器

下面的命令在本仓库根目录执行。`./release-package` 是你准备好的产品发布目录，需要替换为实际位置：

```sh
python3 scripts/check_launch.py --root ./release-package --manifest launch-manifest.json --report ./launch-check.json
```

Windows 可将 `python3` 换成 `py -3`。检查器默认只读发布目录，只在显式传入 `--report` 时写报告；检查通过返回退出码 `0`，发现问题返回 `1`。

| 可以检查 | 不能据此证明 |
| --- | --- |
| 清单引用的文件是否存在、路径是否越界 | 产品承诺是否真实、许可是否充分 |
| PNG 头部尺寸是否与清单一致、素材大小 | 图片完整解码、视觉质量或界面体验 |
| README 中的本地链接 | 外部网页、下载地址是否可用 |
| 部分 Token、私钥、个人路径及凭据文件名 | 不存在任何秘密、Git 历史安全 |

公开前仍需检查实际图片、播放视频、核对远程链接，并按项目情况补充历史密钥扫描。检查器不会登录 GitHub，也不会上传文件。

## 环境与数据边界

| 能力 | 条件 |
| --- | --- |
| 流程阅读、文案与分镜 | 支持本地文件的 Agent 环境 |
| 程序检查 | Python 3.10+；无需 GUI，可离线执行 |
| 真实界面采集 | 宿主提供浏览器或原生 GUI 能力 |
| 图片与视频制作 | 宿主提供相应制作和渲染能力，可能产生服务费用 |
| GitHub 发布 | 可用网络、安全登录，以及用户明确授权的目标与范围 |

检查器回归测试已在 GitHub Actions 的 Linux、macOS 和 Windows 上通过；Python 版本与运行配置见 [测试工作流](.github/workflows/tests.yml) 和 [首次运行结果](https://github.com/LeeYH95/github-product-launch/actions/runs/40286019608)。脚本测试不等于图像、视频、登录和发布流程在所有宿主都完成了验收。

本地检查不会发送文件。执行已授权的发布时，源码和材料发送到指定 GitHub 仓库；调用图像、视频等外部服务时，相关素材是否外发取决于选用的工具。真实用户笔记、截图、凭据和临时数据不应混入公开材料。

## 开发与验证

```sh
python3 -m unittest discover -s tests -v
```

现有回归测试覆盖正常清单、缺失证据、尺寸错误、路径越界、外部符号链接、敏感值不回显、未知授权和失效本地链接。`evals/` 保留用于 Agent 流程试跑的场景；这些场景本身不构成效果证明，也不表示已测量触发准确率。

```text
github-product-launch/
├── SKILL.md                    # Agent 工作流程
├── README.md                   # 安装与使用
├── LICENSE                     # MIT
├── docs/USAGE.md               # 清单示例与问题排查
├── references/                 # 交付格式、视频、发布与恢复
├── scripts/check_launch.py     # 发布目录检查器
├── tests/                      # 检查器回归测试
└── evals/                      # Agent 流程试跑场景
```

## 来源与许可

本 Skill 最初在一个软件项目的发布工作中整理，现在以独立仓库维护，可用于其他已有软件项目。Skill 的代码和文档采用 [MIT License](LICENSE)；使用时引入的项目源码、截图、音乐、字体和其他素材仍需分别核对许可。
