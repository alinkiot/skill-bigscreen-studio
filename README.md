# skill-bigscreen-studio

对话式生成通用/商业可视化大屏的 AI Skill。用户描述业务主题，Skill 引导 AI 完成"脚手架 → 写数据与场景 → 构建 → 截图自检 → 输出"，产出可双击离线打开的 1920×1080 单文件 HTML。

适用于销售、运营、物流、政务、园区、港口等以"看数据"为主的场景，不含设备控制和操作防误。需要 SCADA/HMI 一类工业控制界面时，请使用姊妹仓库 [skill-industrial-screen](https://github.com/alinkiot/skill-industrial-screen)。

## 预设

| 预设 | 形态 | 适用 |
| --- | --- | --- |
| `flat` | 纯数据看板：KPI、主趋势、排行/占比/事件、三小图，无 WebGL | 销售、运营、物流、金融、政务等，默认选它 |
| `observatory` | 3D 数字孪生：Three.js 场景、运营面板、场景点选 | 园区、工厂、港口、机场、电站、矿山等有物理空间的题材 |

## 目录结构

```text
SKILL.md          Skill 入口（触发条件、五步工作流、硬规矩）
references/       引擎契约、flat/observatory API、设计系统、行业 playbook
scripts/          engine / prepare / scaffold / verify
assets/flat/      flat 预设模板
out/              示例产物与每次构建的输出目录
preview.webp      预览图
```

## 依赖

- Node.js 18+
- Python 3.10+（含 Pillow），用于引擎构建
- Playwright，用于截图自检（缺失时仍能产出 HTML，只是没有截图和自检）
- 渲染引擎 [screen-picture-demo](https://github.com/zhengweixing/screen-picture-demo)，解析优先级如下：
  1. `--engine` 参数或环境变量 `VIZLAB_ENGINE`
  2. 与本仓库同级的 `../vizlab` 目录（存在即用）
  3. `~/.bigscreen-ai/cache/bigscreen-engine`，不存在时自动 `git clone` 并安装依赖

## 安装

将本仓库放到 AI 工具的 skills 目录下，目录名使用 `bigscreen-studio`，或打包为 zip 上传：

```bash
git clone git@github.com:alinkiot/skill-bigscreen-studio.git bigscreen-studio
```

## 用法

```bash
# 1. 脚手架（自动准备引擎）
node scripts/scaffold.mjs --id port-ops --preset observatory \
  --title "智慧港口运营中心" --label "PORT NEXUS" \
  --desc "连接泊位、船舶与作业设备，让港口吞吐一目了然。" \
  --category operations --tags "港口,泊位调度,数字孪生"

# 2. 修改引擎 src/screens/<id>/ 下的数据与场景（flat 改 app.js 的 DATA）

# 3. 构建 + 截图自检 + 出包
node scripts/verify.mjs --id port-ops

# 可选：预热引擎
node scripts/prepare.mjs
```

常用开关：`--update` 拉取引擎最新代码，`--out <dir>` 改输出目录，`--no-build`、`--keep-build`、`--bake-preview` 见 `references/engine.md`。

## 产物（`out/`）

```text
out/<id>/<id>.html          单文件大屏，依赖全内联，离线可打开
out/<id>/<id>-1920.png      1920×1080 截图
out/<id>/<id>-report.json   自检结论（ok / errors / warnings / audit.overflow）
```

交付标准是 `ok: true` 且 `audit.overflow` 为空。数据均为演示数据，页脚会标注"演示数据 · 本地模拟"，替换 `app.js` 中的 `DATA` 即可接入真实接口。

## 许可

与原 big-screen 工作区一致，见上游仓库 LICENSE。
