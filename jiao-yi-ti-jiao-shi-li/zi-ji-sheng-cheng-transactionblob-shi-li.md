# 自己生成transaction\_blob示例

自己生成transaction\_blob（以Java为例）包含以下步骤：

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

2.填充交易（Transaction）数据结构，并生成transaction\_blob。

> 导入包:import io.bumo.sdk.core.extend.protobuf.Chain;

```text
    Chain.Transaction.Builder builder = Chain.Transaction.newBuilder();
    builder.setSourceAddress("buQsurH1M4rjLkfjzkxR9KXJ6jSu2r9xBNEw");
    builder.setNonce(7);

    builder.setFeeLimit(1000 * 1000);
    builder.setGasPrice(1000);
    builder.setCeilLedgerSeq(0);
    builder.setMetadata(ByteString.copyFromUtf8(""));

    Chain.Operation.Builder operation = builder.addOperationsBuilder();
    operation.setType(Chain.Operation.Type.CREATE_ACCOUNT);

    Chain.OperationCreateAccount.Builder operationCreateAccount = Chain.OperationCreateAccount.newBuilder();
    operationCreateAccount.setDestAddress("buQoP2eRymAcUm3uvWgQ8RnjtrSnXBXfAzsV");
    operationCreateAccount.setInitBalance(10000000);

    Chain.AccountPrivilege.Builder accountPrivilegeBuilder = Chain.AccountPrivilege.newBuilder();
    accountPrivilegeBuilder.setMasterWeight(1);

    Chain.AccountThreshold.Builder accountThresholdBuilder = Chain.AccountThreshold.newBuilder();
    accountThresholdBuilder.setTxThreshold(1);

    accountPrivilegeBuilder.setThresholds(accountThresholdBuilder);
    operationCreateAccount.setPriv(accountPrivilegeBuilder);
    operation.setCreateAccount(operationCreateAccount);
    String transaction_blob = HexFormat.byteToHex(builder.build().toByteArray());

    得到的transaction_blob是：
    0a2462755173757248314d34726a4c6b666a7a6b7852394b584a366a537532723978424e4577100718c0843d20e8073a37080122330a246275516f50326552796d4163556d33757657675138526e6a7472536e58425866417a73561a0608011a0208012880ade204
```

> **注意**：这里的nonce值不是6，没有连续，因此该交易会超时，不会成功。

3.通过私钥对交易（transaction\_blob）签名。

> 导入包:import io.bumo.encryption.key.PrivateKey;

```text
私钥是：
privbvTuL1k8z27i9eyBrFDUvAVVCSxKeLtzjMMZEqimFwbNchnejS81

签名后的sign_data是：
9C86CE621A1C9368E93F332C55FDF423C087631B51E95381B80F81044714E3CE3DCF5E4634E5BE77B12ABD3C54554E834A30643ADA80D19A4A3C924D0B3FA601
```

4.完成交易数据填充。

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

5.通过POST提交交易。

```text
     POST http://seed1.bumotest.io/submitTransaction
```

得到的响应报文：

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

> **说明**："success\_count":1表明交易提交成功。

