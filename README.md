# TechJournal · 技术学习手记

记录技术学习与实践过程，分享源码阅读、原理理解和实验验证中的收获。

作者：[CedricZ1001](https://github.com/CedricZ1001) · 许可：[CC BY 4.0](LICENSE)

这里会持续整理 Unreal Engine、C++、计算机图形学及其他技术主题的文章。每篇文章尽量从一个具体问题出发，记录理解过程、证据和仍待验证的问题。

## 文章目录

| 主题 | 文章 | 状态 |
| --- | --- | --- |
| Unreal Engine | [UE 一帧画面是怎样生成的？从游戏线程到渲染线程的学习记录](articles/unreal-engine/frame-from-game-thread-to-screen/README.md) | 大纲 / 学习中 |

## 仓库结构

```text
TechJournal/
├── articles/                        # 按主题组织的文章
│   └── unreal-engine/
│       └── frame-from-game-thread-to-screen/
│           ├── README.md            # 文章正文或大纲
│           └── assets/              # 该篇文章的配图与来源记录
├── templates/
│   └── article.md                   # 通用文章模板
├── WRITING.md                       # 写作、配图与发布约定
├── LICENSE                          # CC BY 4.0 完整许可文本
├── .editorconfig
├── .gitattributes
├── .gitignore
└── README.md
```

## 开始写作

1. 在 `articles/<主题>/<文章名>/` 下创建文章目录，目录名使用小写英文和连字符。
2. 复制 [文章模板](templates/article.md)，保存为新目录中的 `README.md`，填写标题和文章信息。
3. 将配图放入同目录的 `assets/`，在正文中使用相对路径引用，并记录素材来源。
4. 在上方文章目录添加链接和状态，随着学习进度更新正文。

详细约定见 [写作指南](WRITING.md)。本仓库使用 Markdown，无需安装依赖或运行构建命令。

## 阅读与纠错

文章是持续更新的学习记录。版本差异、未经验证的推测和未完成的实验会在文中说明。欢迎通过 Issue 或 Pull Request 提出问题、补充依据和修正错误。

## 版权与许可

Copyright © 2026 CedricZ1001.

除另有注明外，本仓库中作者原创且有权授权的内容采用 **Creative Commons Attribution 4.0 International（CC BY 4.0，知识共享署名 4.0 国际许可协议）**。

你可以复制、分享、修改和商业使用这些内容。分享时需按协议保留适当署名、已有版权及许可等声明，提供许可链接和合理可行的原文链接，并注明是否做过修改。材料按原样提供，不作担保；完整条件以 [LICENSE](LICENSE) 为准。

转载署名示例（将文章标题和链接替换为实际值）：

> 《文章标题》，作者 CedricZ1001，原文：本篇文章的 GitHub 链接。采用 CC BY 4.0 许可：https://creativecommons.org/licenses/by/4.0/ 。修改情况：未修改 / 已翻译或节选并说明修改。

文中引用的引擎源码、第三方图片和其他外部材料，仍遵循各自的版权与许可，不因收录在本仓库中而改用 CC BY 4.0。

许可资料：[中文摘要](https://creativecommons.org/licenses/by/4.0/deed.zh-hans) · [完整条款](https://creativecommons.org/licenses/by/4.0/legalcode) · [本地许可原文](LICENSE)
