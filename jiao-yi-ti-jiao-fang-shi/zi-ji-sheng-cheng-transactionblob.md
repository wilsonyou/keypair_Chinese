# 自己生成transaction\_blob

自己生成transaction\_blob、签名，并提交交易，具体操作包括以下步骤：

1.通过调用getAccount接口获取待发起交易的账户的nonce值，如下所示：

```text
          HTTP GET host:port/getAccount?address=账户地址
```

2.填充protocol buffer的交易对象Transaction，并进行序列化操作，从而得到transaction\_blob。具体的交易数据结构详情请看[ProtoBuf数据结构](../protobuf-shu-ju-jie-gou/)。

3.签名交易，并填充交易数据。根据私钥生成公钥，并用私钥对transaction\_blob签名，然后填充提交交易的json数据，格式如下：

```text
    {
        "items" : [{
            "transaction_blob" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", //一个交易序列化之后的16进制表示
            "signatures" : [{//第一个签名
                "sign_data" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", //签名数据
                "public_key" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" //公钥
            }, {//第二个签名
                "sign_data" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", //签名数据
                "public_key" : "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" //公钥
            }
            ]
        }
        ]
    }
```

4.通过调用submitTransaction接口，将第3步生成的json数据作为参数传入，完成交易提交。响应结果格式如下：

```text
    {
        "results": [
        {
            "error_code": 0,
            "error_desc": "",
            "hash": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" //交易的hash
        }
        ],
        "success_count": 1
    }
```

