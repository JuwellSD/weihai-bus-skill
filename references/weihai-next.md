# weihai next-at-stop / next — 下一班车预报

两个子命令分别从"站"和"线路-站"两个粒度查下一班车到站预报。

---

## weihai next-at-stop <stopId> — 站点全部线路下一班

### input

| 参数 | 位置/选项 | 必填 | 默认 | 说明 |
|---|---|---|---|---|
| `stop_id` | 位置参数 | ✅ | — | 站点数字 stopId(来自 `stops`/`nearby`) |
| `--json` | 选项 | | `false` | 输出 JSON |
| `--city` / `--platform` / `--timeout` | 选项 | | `371000` / `2` / `10.0` | 通用 |

### action

用户给一个 stopId,想知道**经过该站的所有线路**各自的下一班车预计到站时间。

```bash
weihai next-at-stop 3710000100094159 --json
```

### output

每条途径线路一行:`<站名>: <预报>`。`--json` 返回 NextBus 数组,含 `stop_name / no_bus_desc / buses[] / alert` 等。

### 失败

- 该站无线路 → `(该站点无线路数据)`,退出码 0
- 夜间无车辆 → `buses` 为空、`no_bus_desc` 如"非车辆运营时间",属正常

---

## weihai next <routeId> <stopId> — 某线路在某站的下一班

### input

| 参数 | 位置/选项 | 必填 | 默认 | 说明 |
|---|---|---|---|---|
| `route_id` | 位置参数 | ✅ | — | 线路数字 routeId |
| `stop_id` | 位置参数 | ✅ | — | 站点数字 stopId |
| `--json` | 选项 | | `false` | 输出 JSON |
| `--city` / `--platform` / `--timeout` | 选项 | | `371000` / `2` / `10.0` | 通用 |

### action

用户在一条确定线路(schema 步行到某个具体站)想知道该站下一班,比 `next-at-stop` 更精准(限定线路)。

```bash
weihai next 3710000100564252 3710000100094159
```

### output

`<站名>: <预报>`。`--json` 返回单个 `{nextBuses:{...}, stops:[...]}` 或 `null`。

### 失败

- 无数据 → 输出 `(无数据)`,退出码 0
- 夜间/非运营 → `nextBuses` 只含 `{alert, type}`,站名/预报为空,属正常