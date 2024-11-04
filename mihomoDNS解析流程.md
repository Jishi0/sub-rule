分流：
```mermaid
flowchart TD
  Start[客户端发起请求] --> rule[匹配规则]
  rule --> Domain[匹配命中到基于域名的规则]
  rule --> IP[匹配到基于 IP 的规则]

  Domain --> |域名匹配命中到代理规则|Remote[通过代理服务器解析域名并建立连接]
  Domain --> |域名匹配命中到直连规则|DNS1[DNS请求真IP]
  DNS1 --> Direct[通过 IP 直接建立连接，（如配置了direct-nameserver则使用其解析）]

  IP --> |IP匹配命中到直连规则|Direct
  IP --> |IP匹配命中到代理规则|Proxy[通过 IP 经代理建立连接]
  IP --> |请求只包含域名|DNS2[DNS请求真IP]
  DNS2 --> |继续匹配|IP

```

DNS：
```mermaid
flowchart TD
  DNS[通过 Clash DNS 解析域名] --> FakeIP
  FakeIP --> |查询 DNS 缓存|Cache

  Cache --> |Cache 命中，FakeIP-Direct 命中|Get[得到真IP]
  Cache --> |Cache 命中，FakeIP-Direct 未命中|NS[匹配 nameserver-policy 并查询 ]
  Cache --> |Cache 未命中|FakeGet[得到假IP]

  NS --> |匹配成功| Get
  NS --> |匹配失败| NF[nameserver/fallback 并发查询]
  NF --> Get

  Get --> |缓存 DNS 结果|Cache
  FakeGet --> |缓存 DNS 结果|Cache

```

