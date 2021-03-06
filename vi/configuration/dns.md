---
refcn: chapter_02/04_dns
refen: configuration/dns
---
# DNS

V2Ray có một máy chủ DNS nội bộ cung cấp chuyển tiếp DNS cho các thành phần khác.

{% hint style='info' %}

Do sự phức tạp của giao thức DNS, V2Ray hiện chỉ hỗ trợ các truy vấn IP cơ bản (A và AAAA). Chúng tôi khuyên bạn nên sử dụng một DNS chuyên nghiệp dựa (chẳng hạn như [CoreDNS](https://coredns.io/)) cho V2Ray.

{% endhint %}

Các truy vấn DNS được chuyển tiếp bởi dịch vụ DNS này cũng sẽ được gửi đi dựa trên các thiết lập định tuyến. Không cần cấu hình thêm.

## DnsObject

`DnsObject` được sử dụng làm trường `dns` trong cấu hình mức cao nhất.

```javascript
{
  "hosts": {
    "baidu.com": "127.0.0.1"
  },
  "servers": [
    {
      "address": "1.2.3.4",
      "port": 5353,
      "domains": [
        "domain:v2ray.com"
      ]
    },
    "8.8.8.8",
    "8.8.4.4",
    "localhost"
  ],
  "clientIp": "1.2.3.4",
  "tag": "dns_inbound"
}
```

> `hosts`: map{string: address}

Một danh sách các địa chỉ IP tĩnh. Mỗi mục có tên miền là khóa và địa chỉ IP làm giá trị. Nếu truy vấn DNS nhắm mục tiêu một trong các tên miền trong danh sách này, IP tương ứng sẽ được trả về ngay lập tức và truy vấn DNS sẽ không được chuyển tiếp.

The format of domains is the same as it in [routing](routing.md#ruleobject).

> `servers`: \[string | [ServerObject](#serverobject) | "localhost" \]

Danh sách các máy chủ DNS. Mỗi máy chủ có thể được chỉ định theo ba định dạng: địa chỉ IP, [ServerObject](#serverobject)hoặc `"localhost"`.

Khi máy chủ là địa chỉ IP, chẳng hạn như `"8.8.8.8"`, V2Ray sẽ truy vấn DNS trên cổng UDP 53 trên địa chỉ này.

Khi máy chủ là `"localhost"`, V2Ray sẽ truy vấn máy chủ cục bộ cho DNS.

{% hint style='info' %}

Khi `"localhost"` được sử dụng, lưu lượng truy cập DNS không được kiểm soát bởi V2Ray. Tuy nhiên, bạn có thể chuyển hướng truy vấn DNS trở lại V2Ray với cấu hình bổ sung.

{% endhint %}

> `clientIp`: string

Địa chỉ IP của máy hiện tại. Nếu được chỉ định, V2Ray sử dụng IP này làm EDNS-Client-Subnet. Địa chỉ IP này không thể là địa chỉ riêng tư.

> `tag`: string

(V2Ray 4.13+) All traffic initiated from this DNS, except to localhost, will have this tag as inbound. It can be used for routing.

### ServerObject

```javascript
{
  "address": "1.2.3.4",
  "port": 5353,
  "domains": [
    "domain:v2ray.com"
  ],
}
```

> `address`: address

Address of the DNS server. For now only UDP servers are supported.

> `port`: number

Port of the DNS server. Usually it is `53` or `5353`.

> `domains`: \[string\]

A list of domains. If the domain of enquire matches one of the list, this DNS server will be prioritized for DNS query for this domain.

Domain name format is the same as in [routing](routing.md).

When a DNS server has the domain in its domain list, the domain will be queried in this server first, and then other servers. Otherwise DNS queries are sent to DNS servers in the order they appear in the config file.