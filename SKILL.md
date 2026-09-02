---
name: weihai-bus
version: 1.0.0
description: "威海实时公交查询 CLI (weihai)。封装 timebus.ibuscloud.com 的免授权只读 API,支持线路/站点搜索、车辆实时位置、附近站点、下一班车四种能力。优先调用 `weihai <command>` 而不是直接访问 API。默认 city=371000、h5Platform=2(免授权)。"
metadata:
  requires:
    bins: ["weihai"]
  cliHelp: "weihai --help"
---

# weihai-bus — 威海实时公交 CLI

**CRITICAL — 所有命令都通过 `weihai` 命令执行,不要直接 curl API。**
**CRITICAL — 没有现成子命令覆盖时,再考虑用 `weihai ... --json` 取结构化数据拼接,而非写脚本。**

## 使用原则

- 用户问威海(或其他 supported 城市)公交线路、站点、车辆位置、到站时间,一律走 `weihai`
- 默认免授权直连,无需登录/ token
- 每个命令都支持 `--json` 输出结构化数据,便于脚本与二次加工
- 本 skill 只读,不提供任何写出/抓取/批量爬取能力,不作为广告或无重限流用途

## 适用场景

- 查某公交线路有哪些方向、routeId、站点数、首末班时间
- 查某站点的 stopId、经纬度、途经线路
- 查某线路当前在线车辆、经停站点序列
- 查约定位置附近 1000m 内有哪些公交站
- 查某站点或某线路-站点的下一班车预计到达时间
- 从 CLI 单条命令快速拿结构化 JSON 汇入报表 / 地图 / 别的程序

## 反唤起信号

- 用户要登录、鉴权、生成 token:本 skill 始终免授权,转 arkcli-auth(无关) 或说明无需登录
- 用户要批量爬全量数据库、高并发轮询、抓静态快照:拒绝,这是只读工具不承担数据采集
- 用户要查的不是公交而是轨道交通/铁运/城际:本 API 只覆盖公交,说明范围
- 用户要改数据、上传、提交反馈:CLI 无写命令(源码 SDK 有 addCorrection 但非 CLI 能力)

## 前置要求

- 需要 `weihai` 命令可用。若缺失:在 `C:\Users\Zihao\Desktop\ZD\weihai-bus-sdk` 下 `uv sync` 后
  用 `.venv\Scripts\weihai.exe`;或 `uv tool install weihai-bus` 全局安装。
- CLI 输出为 UTF-8;在 Windows 命令行若乱码,先设 `PYTHONIOENCODING=utf-8`。

## 命令索引

| 目标 | 命令 | 说明 |
|---|---|---|
| 查线路 | `weihai routes <名称> [-v] [--json]` | 线路方向 / routeId / 站数 / 首末班 |
| 车辆实时 | `weihai vehicles --name <线路> 或 <routeId> [--json]` | 全程经停 + 每站车辆数 |
| 查站点 | `weihai stops <名称> [-r] [--json]` | stopId / 经纬度 / 途经线路 |
| 附近站点 | `weihai nearby --radius <米> [--lat --lng] [--json]` | 半径内站点 + routeCount |
| 站点下一班 | `weihai next-at-stop <stopId> [--json]` | 该站全部线路到站预报 |
| 线路-站点下一班 | `weihai next <routeId> <stopId> [--json]` | 特定线路在该站到站预报 |

所有命令可选 `--city <码/type>` 与 `--platform <值>` 切换城市;默认威海 `--city 371000 --platform 2`。

## 通用输出约定

- 人读模式:线路列出 routeId;站点列出 stopId 与经纬度
- 缺省值事件:夜间(非运营时段)车辆 / 到站数据为空,命令正常返回"无数据"或空列表,非报错
- `--json`:`ensure_ascii=False`,中文原样输出,key 为 snake_case(pydantic 模型字段)

## 执行提示

命令执行前先根据下表选 reference 文档,路径相对本 SKILL.md,`references/` 下:

- **routes**:读取 [`references/weihai-routes.md`](references/weihai-routes.md)
- **vehicles**:读取 [`references/weihai-vehicles.md`](references/weihai-vehicles.md)
- **stops / nearby**:`stops` 读 [`references/weihai-stops.md`](references/weihai-stops.md)
- **next-at-stop / next**:读取 [`references/weihai-next.md`](references/weihai-next.md)