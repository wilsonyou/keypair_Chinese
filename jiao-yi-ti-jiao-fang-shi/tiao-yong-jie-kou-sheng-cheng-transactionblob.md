# 调用接口生成transaction\_blob

> **注意**：由于transaction\_blob很可能被截取和篡改，因此不建议用这种方式生成transaction\_blob。

如果需要调用接口生成transaction\_blob、签名并提交交易，请查看 BUMO 的开发文档，地址如下：

[https://github.com/bumoproject/bumo/blob/master/docs/develop.md](https://github.com/bumoproject/bumo/blob/master/docs/develop.md)

调用接口生成transation\_blob包含以下步骤：

1.调用getAccount接口获取待发起交易账户的nonce值，代码如下所示：

```text
    HTTP GET host:port/getAccount?address=账户地址
```

2.根据需要填充json数据并完成交易数据填充，格式如下所示：

```text
    {
        "source_address":"xxxxxxxxxxx", //交易源账号，即交易的发起方
        "nonce":2, //nonce的值
        "ceil_ledger_seq": 0, //可选
        "fee_limit":1000, //交易支付的费用
        "gas_price": 1000, //gas价格(不小于配置的最低值)
        "metadata":"0123456789abcdef", //可选，用户自定义给交易的备注，16进制格式
        "operations":[
        {
            //根据不同的操作填写
        },
        {
            //根据不同的操作填写
        }
            ......
        ]
    }
```

> **注意**：nonce值需要在第1步中获取值的基础上加1。

3.通过调用getTransactionBlob接口将第2步中生成的json数据作为参数传入，得到一个交易hash和transaction\_blob，实现交易序列化，格式如下所示：

```text
    {
        "error_code": 0,
        "error_desc": "",
        "result": {
            "hash": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", //交易的hash
            "transaction_blob": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" //交易序列化之后的16进制表示
        }
    }
```

4.对交易进行签名并填充交易数据。根据之前生成的私钥对transaction\_blob签名，然后填充提交交易的json数据，格式如下所示：

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

5.通过调用submitTransaction接口，将第4步中生成的json数据作为参数传入，得到响应结果，完成交易提交。响应结果的格式如下所示：

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

