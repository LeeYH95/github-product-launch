# 使用示例与问题排查

## 从一个现成软件开始

给 Agent 的请求可以是：

> 用 github-product-launch 给这个已经能运行的软件准备 GitHub 发布材料。先在本地完成，展示真实界面，重点解释第一次使用的步骤；上传目标尚未确定。

Agent 应先阅读项目和许可，确认实际功能、构建入口、已有品牌与演示方式。能继续准备的工作不应因仓库名称未确定而停止；远程创建和上传要等目标与授权明确后执行。

根据项目需要，交付目录可以是：

```text
release-package/
├── README.md
├── launch-manifest.json
├── src/                         # 准备公开的源码
├── docs/media/                  # Banner、功能图、视频和封面
├── docs/launch/                 # 发布介绍、分镜、Release 说明
└── marketing/source/            # 可重新制作媒体的源文件
```

这只是结构示例，不要求机械创建所有文件。私有数据、缓存和运行日志留在目录外。源码与合理大小的介绍素材可以进入仓库，安装包通常作为 Release 附件。

## 怎样填写清单

下面是**虚构导出工具的格式示例**，没有提供对应软件或图片。把产品名、文件路径、尺寸和来源替换为真实内容后才能检查：

```json
{
  "product": {
    "name": "Example Exporter",
    "summary": "把本地记录导出为 Markdown",
    "repository": "https://github.com/owner/example-exporter"
  },
  "claims": [
    {
      "text": "支持 Markdown 导出",
      "evidence": ["src/export.py"]
    }
  ],
  "assets": [
    {
      "path": "docs/media/banner.png",
      "role": "banner",
      "origin": "composition",
      "source": "marketing/source/banner.html",
      "rights": "project-owned; fictional demo content",
      "width": 1600,
      "height": 900
    }
  ],
  "readmes": ["README.md"],
  "max_asset_bytes": 25000000
}
```

`evidence` 是发布目录内能支持承诺的文件；`source` 是可定位的素材来源，可以是目录内文件，也可以是已核实的 HTTPS 来源。程序只检查证据文件是否存在，不能理解一个不相关文件是否足以支持宣传文案。

所有本地路径相对 `--root`，不应指向外部个人目录。`rights` 必须记录实际授权依据；不能写“未知”后继续公开。

## 怎样理解报告

成功报告包含 `ok: true`、素材与承诺数量，以及检查器的能力边界。失败报告的 `errors` 给出规则名和文件路径，例如：

| 规则 | 处理方法 |
| --- | --- |
| `missing-file` | 修正清单或 README 路径，或补齐真正需要交付的文件 |
| `unsafe-path` / `external-symlink` | 将准备公开的内容放进发布目录，移除对目录外文件的引用 |
| `png-dimensions` | 检查实际导出尺寸，并更新图片或清单 |
| `unresolved-rights` | 确认素材可公开分发的授权，或换用有依据的素材 |
| `github-token` / `private-key` / `credential-file` | 停止发布并检查文件；若是真实凭据已泄露，先撤销或轮换 |
| `personal-path` | 检查是否将个人路径写进公开材料，改用相对路径或通用示例 |

不要为了让检查器通过而删掉有效证据、伪造授权或掩盖风险。规则检查会漏报，也可能匹配到测试用的假凭据，需要结合上下文判断。

## 环境缺少能力时

- **没有视频渲染器**：保留分镜、字幕和素材表，标记 MP4 未生成。
- **没有图形界面**：先完成事实清单和说明；不要虚构已采集的真实界面。
- **没有网络或 GitHub 登录**：完成本地材料与检查，保留目标仓库信息；恢复登录后先读远程状态，再继续未完成步骤。
- **已有仓库或 Release**：核对所有者、分支、版本和现有文件，避免重建或覆盖其他结果。
- **只更新一个版本**：复用清单和有效素材，核对新功能证据，再更新关联文案与下载地址。

## 发布前的人工检查

读一遍 README，确认用户能理解用途和开始步骤；打开每张导出的图片，完整播放视频；检查演示中没有个人内容；最后核对 GitHub 上的默认分支、素材、安装说明和 Release 下载。程序测试、素材审阅与真实用户体验是不同证据，不应互相替代。
