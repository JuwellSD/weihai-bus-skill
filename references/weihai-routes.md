# weihai routes — 按线路名搜索线路

对应 CLI 子命令:`weihai routes <名称> [选项]`

## input

| 参数 | 位置/选项 | 必填 | 默认 | 说明 |
|---|---|---|---|---|
| `name` | 位置参数 | ✅ | — | 线路名关键字,如 `1路`、`K1`。返回所有含该关键字的线路与方向 |
| `--verbose` / `-v` | 选项 | | `false` | 追加显示起终点、站数、首末班时间 |
| `--json` | 选项 | | `false` | 输出结构化 JSON |
| `--city` | 选项 | | `371000` | 城市码或 type 值 |
| `--platform` | 选项 | | `2` | h5Platform,免授权直连 |
| `--timeout` | 选项 | | `10.0` | 请求超时秒数 |

## action

命中:用户给一个公交线路名/关键字,想确认它的 routeId、方向、站点规模或运营时间。

标准用法:

```bash
weihai routes 1路 -v
weihai routes K1 --json
```

## output

人读模式每行一个线路方向,例:

```
1路  routeId=3710000100128319  江家寨立交桥 -> 实验中学  站数=34  首/末=05:48:00/20:30:00
1路  routeId=3710000100128318  实验中学 -> 江家寨立交桥  站数=35  首/末=05:45:00/21:40:00
```

`--json` 返回数组,每项 `{name, routes:[Route]}`。Route 含 `route_id / opposite_id / route_name / amap_id / origin / terminal / first_bus / last_bus / station_cnt / direction` 等(字段为 snake_case)。

同一条线路名可能有**两个方向**(routeId 成对出现),也有同名支线(如 `文登1路`)。取车辆位置/下一班时,先确认要哪个 routeId。

## side_effect

- 只读查询,无副作用;不上传、不写库
- 每个方向都是一个独立 `routeId`,`vehicles` / `next` 需要它

## 失败处理

- 无匹配 → 输出 `(无结果)`,退出码 0
- 网络/服务端错误 → 非零退出码 + 错误信息;确认网络与 `--city/--platform` 是否有效