# ikuai-cn-ip v2（新版爱快格式）

本项目基于 [ikuai-cn-ip](../ikuai-cn-ip/) 重构，生成适配**新版爱快（iKuai）系统**导入规范的国内 IPv4、IPv6 及分省 IPv4 地址组文件。旧版项目保留在同级目录，仍可用于旧版系统。

## 输出格式

新版爱快使用 `group_value` 字段承载 JSON 数组，不再使用旧版的 `comment=` / `addr_pool=` 字段：

```text
id=60 group_name=国内IPV4-1 group_value=[{"ip":"1.0.1.0\/24","comment":""},{"ip":"1.0.2.0\/23","comment":""}]
id=80 group_name=国内IPV6-1 group_value=[{"ipv6":"2001:250:2000::\/35","comment":""}]
```

- IPv4 条目：`{"ip":"x.x.x.x/xx","comment":""}`
- IPv6 条目：`{"ipv6":"xxxx::/xx","comment":""}`
- JSON 中 `/` 按系统要求转义为 `\/`
- 每组最多 1000 条，超出自动拆分为 `-1`、`-2` ……

## 生成文件

| 文件名 | 内容 | ID 起始 | 组名示例 |
|---|---|---|---|
| `ikuai_cn_ipv4group.txt` | 国内 IPv4 聚合分组 | 60 | `国内IPV4-1` |
| `ikuai_cn_ipv6group.txt` | 国内 IPv6 聚合分组 | 80 | `国内IPV6-1` |
| `ikuai_cn_province_ipgroup.txt` | 分省 IPv4 分组 | 60 | `安徽IP`、`北京IP(1)` |

> 说明：分省 IPv4 与全国 IPv4 内容重复，二选一导入即可，因此共用起始 ID 60 不会冲突。

## 数据源

- IPv4：
  - `https://metowolf.github.io/iplist/data/special/china.txt`
  - `https://cdn.jsdelivr.net/gh/Loyalsoldier/geoip@release/text/cn.txt`
- IPv6：
  - `https://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest`
  - `https://raw.githubusercontent.com/mayaxcn/china-ip-list/refs/heads/master/chn_ip_v6.txt`
- 分省 IPv4：
  - `https://raw.githubusercontent.com/metowolf/iplist/master/data/country/CN/CN-<省简称>.txt`

## 使用方法

1. 安装依赖：`pip install -r requirements.txt`
2. 生成全国 IPv4/IPv6：`python ikuai_ipgroups.py`
3. 生成分省 IPv4：`python ikai_cni_p.py`
4. 在新版爱快后台的 IP 分组导入功能中，分别导入生成的三份文件。

## 自动更新

通过 GitHub Actions 每日自动更新：

- `.github/workflows/ik-cn-ip.yml`：每天北京时间 08:00（UTC 00:00）更新全国 IPv4/IPv6。
- `.github/workflows/ik-cn-province.yml`：每天北京时间 02:00（UTC 18:00）更新分省 IPv4。

## 依赖

- Python 3.10+
- `requests`
