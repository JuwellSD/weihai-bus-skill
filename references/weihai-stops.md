# weihai stops / nearby — 站点定位

两个子命令覆盖"按名找站"与"按距找站"两类需求。

---

## weihai stops <名称> — 按站点名搜索

### input

| 参数 | 位置/选项 | 必填 | 默认 | 说明 |
|---|---|---|---|---|
| `name` | 位置参数 | ✅ | — | 站点名关键字,如 `威海九中`、`大世界` |
| `--routes` / `-r` | 选项 | | `false` | 同时列出途经该站的线路与 routeId |
| `--json` | 选项 | | `false` | 输出 JSON |
| `--city` / `--platform` / `--timeout` | 选项 | | `371000` / `2` / `10.0` | 通用 |

### action

用户给一个站名,想知道它的 stopId、经纬度、是否有多方向站点、途经哪些线路。

```bash
weihai stops 威海九中
weihai stops 大世界 --routes --json
```

### output

人读模式每行一个同名站点(同名站可有多个 `stopId`,对应不同乘车方向):

```
威海九中  stopId=3710000100094159  (37.5077, 122.0682)
威海九中  stopId=3710000100094160  (37.5074, 122.0685)
```

`--routes` 在每个站点下缩进列出途经线路。`--json` 返回 `[{name, stops:[...]}]`,Stop 含 `stop_id/stop_name/lat/lng/city_code` 等。

### 失败

- 无匹配 → 输出 `(无结果)`,退出码 0

---

## weihai nearby --radius <米> — 附近站点

### input

| 参数 | 位置/选项 | 必填 | 默认 | 说明 |
|---|---|---|---|---|
| `--radius` | 选项 | ✅ | `1000` | 搜索半径(米)。强烈建议显式传以避免默认误解 |
| `--lat` | 选项 | | 城市中心纬度 | 指定中心纬度 |
| `--lng` | 选项 | | 城市中心经度 | 指定中心经度 |
| `--routes` / `-r` | 选项 | | `false` | 每站显示 routeCount |
| `--json` | 选项 | | `false` | 输出 JSON |
| `--city` / `--platform` / `--timeout` | 选项 | | `371000` / `2` / `10.0` | 通用 |

### action

用户想知道某坐标/市中心附近有哪些公交站。**radius 为后端必填参数**,SKILL 端已给 1000 默认,但按需求显式指定更稳。

```bash
weihai nearby --radius 2000
weihai nearby --radius 500 --lat 37.50 --lng 122.11 --json
```

### output

人读模式按"📍 站点名"分组列出周边站:

```
📍 市妇幼保健院西
    市妇幼保健院西  stopId=3710000100094442  routes=9
```

`--json` 返回 `[{name, stops:[{stop, route_count, routes}]}]`。注意此结构**与 `stops` 不同**(嵌套 `stop` + `route_count`)。

### 失败

- 附近无站 → 输出 `(附近无站点)`,退出码 0
- 缺 radius → 后端报"缺少必填参数[radius]"错误