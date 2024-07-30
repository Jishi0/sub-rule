```mermaid
flowchart TD
  Start[客户端发起请求] --> rule[匹配规则]
  rule -->  Domain[匹配基于域名的规则]
  rule --> IP[匹配基于 IP 的规则]

  Domain --> |域名匹配到代理规则|Remote[通过代理服务器解析域名并建立连接]
  Domain --> |域名匹配到直连规则（入口1）|DNS[通过 Clash DNS 解析域名]
  Domain --> |域名未匹配到规则（入口2）|DNS

  DNS --> FakeIP
  FakeIP --> |查询 DNS 缓存|Cache

  Cache --> |Cache 命中，FakeIP-Direct 命中|Get[查询得到 IP]
  Cache --> |Cache 命中，FakeIP-Direct 未命中|NS[匹配 nameserver-policy 并查询 ]
  Cache --> |Cache 未命中|FakeGet[返回假 IP]

  NS --> |匹配成功| Get
  NS --> |没匹配到| NF[nameserver/fallback 并发查询]
  NF --> Get

  Get --> |缓存 DNS 结果|Cache
  FakeGet --> |缓存 DNS 结果|Cache


  Get --> |出口1（对应入口1）|Direct[通过 IP 直接建立连接]
  Get --> |出口2（对应入口2）|IP

  IP --> |IP匹配到直连规则|Direct
  IP --> |IP匹配到代理规则或未匹配到规则|Proxy[通过 IP 经代理建立连接]



```
