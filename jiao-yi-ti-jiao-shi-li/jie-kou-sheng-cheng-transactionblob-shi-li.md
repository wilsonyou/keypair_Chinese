# 接口生成transaction\_blob示例

通过接口生成transaction\_blob包含以下步骤：

1.通过GET获取待发起交易账户的nonce值。

```text
    GET http://seed1.bumotest.io:26002/getAccount?address=buQsurH1M4rjLkfjzkxR9KXJ6jSu2r9xBNEw
```

得到的响应报文：

```text
    {
            "error_code" : 0,
            "result" : {
                  "address" : "buQsurH1M4rjLkfjzkxR9KXJ6jSu2r9xBNEw",
                  "assets" : [
                  {
                       "amount" : 1000000000,
                       "key" : {
                           "code" : "HNC",
                           "issuer" : "buQBjJD1BSJ7nzAbzdTenAhpFjmxRVEEtmxH"
                       }
                  }
                  ],
                  "assets_hash" : "3bf279af496877a51303e91c36d42d64ba9d414de8c038719b842e6421a9dae0",
                  "balance" : 27034700,
                  "metadatas" : null,
                  "metadatas_hash" : "ad67d57ae19de8068dbcd47282146bd553fe9f684c57c8c114453863ee41abc3",
                  "nonce" : 5,
                  "priv" : {
                        "master_weight" : 1,
                        "thresholds" : [{
                              "tx_threshold" : 1
                        }
                        ]
                  }
           }
    }
    address: 当前查询的账户地址
    assets: 账户资产列表
    assets_hash: 资产列表hash
    balance: 账户资产余额
    metadata: 交易备注，必须是16进制
    metadatas_hash: 交易备注hash
    nonce: 转出方交易序列号，通过查询账户信息接口返回的nonce + 1
    priv: 权限
    master_weight: 当前账户权重
    thresholds: 门限
    tx_threshold: 交易默认门限
```

2.完成交易数据填充。

通过 Keypair 中的 [生成地址](../sheng-cheng-di-zhi.md)生成的新账户B的地址是_buQoP2eRymAcUm3uvWgQ8RnjtrSnXBXfAzsV_，填充的json数据如下：

```text
         {
             "source_address":"buQsurH1M4rjLkfjzkxR9KXJ6jSu2r9xBNEw",
             "nonce":7,
             "ceil_ledger_seq": 0,
             "fee_limit":1000000, 
             "gas_price": 1000,
             "metadata":"",
             "operations":[
             {
                 "type": 1,
                 "create_account": {
                     "dest_address": "buQoP2eRymAcUm3uvWgQ8RnjtrSnXBXfAzsV",
                     "init_balance": 10000000, 
                     "priv": {
                         "master_weight": 1,
                         "thresholds": {
                             "tx_threshold": 1
                         }
                     }
                  }
              }
              ]
}
```

> **注意**：这里的nonce值不是6，没有连续，因此该交易会超时，不会成功。

3.对交易数据进行序列化处理。

```text
         POST http://seed1.bumotest.io:26002/getTransactionBlob
```

请求报文:

```text
4.1.2中填充的json数据
```

响应报文:

```text
{
                "error_code": 0,
                "error_desc": "",
                "result": {
                    "hash": "be4953bce94ecd5c5a19c7c4445d940c6a55fb56370f7f606e127776053b3b51",
                    "transaction_blob": "0a2462755173757248314d34726a4c6b666a7a6b7852394b584a366a537532723978424e4577100718c0843d20e8073a37080122330a246275516f50326552796d4163556d33757657675138526e6a7472536e58425866417a73561a0608011a0208012880ade204"
                 }
}
```

4.通过私钥对交易（transaction\_blob）签名。

> 导入包:import io.bumo.encryption.key.PrivateKey;

```text
私钥是:
privbvTuL1k8z27i9eyBrFDUvAVVCSxKeLtzjMMZEqimFwbNchnejS81

签名后的sign_data是：
9C86CE621A1C9368E93F332C55FDF423C087631B51E95381B80F81044714E3CE3DCF5E4634E5BE77B12ABD3C54554E834A30643ADA80D19A4A3C924D0B3FA601
```

5.完成交易数据填充。

```text
    {
        "items" : [{
            "transaction_blob" : "0a2462755173757248314d34726a4c6b666a7a6b7852394b584a366a537532723978424e4577100718c0843d20e8073a37080122330a246275516f50326552796d4163556d33757657675138526e6a7472536e58425866417a73561a0608011a0208012880ade204",                        
            "signatures" : [{
                "sign_data" : "9C86CE621A1C9368E93F332C55FDF423C087631B51E95381B80F81044714E3CE3DCF5E4634E5BE77B12ABD3C54554E834A30643ADA80D19A4A3C924D0B3FA601",
                "public_key" : "b00179b4adb1d3188aa1b98d6977a837bd4afdbb4813ac65472074fe3a491979bf256ba63895"
             }
             ]
        }
        ]
    }
```

6.通过POST提交交易。

```text
     POST http://seed1.bumotest.io/submitTransaction
```

得到如下的响应报文：

```text
    {
        "results": [{
            "error_code": 0,
            "error_desc": "",
            "hash": "be4953bce94ecd5c5a19c7c4445d940c6a55fb56370f7f606e127776053b3b51"
        }
        ],
        "success_count": 1
    }
```

> **说明**：“success\_count”:1表示提交成功。

