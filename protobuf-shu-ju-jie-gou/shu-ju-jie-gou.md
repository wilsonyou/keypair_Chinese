# 数据结构

下面介绍了交易中可能用到的各种ProtoBuf数据结构及其用途，供用户参考使用。

1.Transaction

该数据结构适用于完整的交易。

```text
    message Transaction {
        enum Limit{
            UNKNOWN = 0;
            OPERATIONS = 1000;
        };
        string source_address = 1;                 // 交易发起账户地址
        int64 nonce = 2;                           // 交易序列号
        int64  fee_limit = 3;                      // 交易费用，默认1000Gas，单位是MO，1 BU = 10^8 MO
        int64  gas_price = 4;                       // 交易打包费用，默认是1000，单位是MO，1 BU = 10^8 MO
        int64 ceil_ledger_seq = 5;                 // 区块高度限制
        bytes metadata = 6;                        // 交易备注
        repeated Operation operations = 7;         // 操作列表
    }
```

2.Operation

该数据结构适用于交易中的操作。

```text
    message Operation {
        enum Type {
            UNKNOWN = 0;
            CREATE_ACCOUNT = 1;
            ISSUE_ASSET = 2;
            PAY_ASSE = 3;
            SET_METADATA = 4;
            SET_SIGNER_WEIGHT = 5;
            SET_THRESHOLD = 6;
            PAY_COIN = 7;
            LOG = 8;
            SET_PRIVILEGE = 9;
        };
        Type type = 1;                             // 操作类型 
        string source_address = 2;                 // 操作源账户地址
        bytes metadata = 3;                        // 操作备注

        OperationCreateAccount create_account = 4;            // 创建账户操作
        OperationIssueAsset issue_asset = 5;                  // 发行资产操作
        OperationPayAsset pay_asset = 6;                      // 转移资产操作
        OperationSetMetadata set_metadata = 7;                // 设置metadata
        OperationSetSignerWeight set_signer_weight = 8;       // 设置签名者权限
        OperationSetThreshold     set_threshold = 9;           // 设置交易门限
        OperationPayCoin pay_coin = 10;                       // 转移coin
        OperationLog log = 11;                                // 记录log
        OperationSetPrivilege set_privilege = 12;             // 设置权限
     }
```

3.OperationCreateAccount

该数据结构用于创建账户。

```text
    message OperationCreateAccount{
        string dest_address = 1;                  // 待创建的目标账户地址
        Contract contract = 2;                    // 合约
        AccountPrivilege priv = 3;                // 权限
        repeated KeyPair metadatas = 4;           // 附加信息
        int64    init_balance = 5;                 // 初始化余额
        string  init_input = 6;                   // 合约入参
    }
```

4.Contract

该数据结构用于设置合约。

```text
    message Contract{
        enum ContractType{
            JAVASCRIPT = 0;
        }
        ContractType type = 1;                   // 合约类型
        string payload = 2;                      // 合约代码
    }
```

5.AccountPrivilege

该数据结构用于设置账户权限。

```text
    message AccountPrivilege {
        int64 master_weight = 1;                 // 账户自身权重
        repeated Signer signers = 2;             // 签名者权重列表
        AccountThreshold thresholds = 3;         // 门限
    }
```

6.Signer

该数据结构用于设置签名者权重。

```text
    message Signer {
        enum Limit{
            SIGNER_NONE = 0;
            SIGNER = 100;
        };
        string address = 1;                      // 签名者账户地址
        int64 weight = 2;                        // 签名者权重
    }
```

7.AccountThreshold

该数据结构用于设置账户门限。

```text
    message AccountThreshold{
        int64 tx_threshold = 1;                                 // 交易门限
        repeated OperationTypeThreshold type_thresholds = 2;    // 指定操作的交易门限列表，未指定的操作的交易以tx_threshold为门限
    }
```

8.OperationTypeThreshold

该数据结构用于指定类型的操作门限。

```text
    message OperationTypeThreshold{
        Operation.Type type = 1;                 // 操作类型
        int64 threshold = 2;                     // 该操作对应的门限
    }
```

9.OperationIssueAsset

该数据结构用于发行资产。

```text
    message OperationIssueAsset{
        string code = 1;                         // 待发行的资产编码
        int64 amount = 2;                        // 待发行的资产数量
    }
```

10.OperationPayAsset

该数据结构用于转移资产。

```text
    message OperationPayAsset {
        string dest_address = 1;                 // 目标账户地址
        Asset asset = 2;                         // 资产
        string input = 3;                        // 合约入参
    }
```

11.Asset

该数据结构适用于资产。

```text
    message Asset{
        AssetKey    key = 1;                      // 资产标识
        int64        amount = 2;                   // 资产数量
    }
```

12.AssetKey

该数据结构用于标识资产唯一性。

```text
    message AssetKey{
        string issuer = 1;                       //  资产发行账户地址
        string code = 2;                         //  资产编码
        int32 type = 3;                          //  资产类型（默认为0，表示不限制数量）
    }
```

13.OperationSetMetadata

该数据结构用于设置Metadata。

```text
    message OperationSetMetadata{
        string    key = 1;                         // 关键字，惟一
        string  value = 2;                       // 内容
        int64     version = 3;                     // 版本控制，可不设置
        bool    delete_flag = 4;                 // 是否删除
    }
```

14.OperationSetSignerWeight

该数据结构用于设置签名者权重。

```text
    message OperationSetSignerWeight{
        int64 master_weight = 1;                 // 自身权重
        repeated Signer signers = 2;             // 签名者权重列表
    }
```

15.OperationSetThreshold

该数据结构用于设置门限。

```text
    message OperationSetThreshold{
        int64 tx_threshold = 1;                               // 交易门限
        repeated OperationTypeThreshold type_thresholds = 2;  // 指定操作的交易门限列表，未指定的操作的交易以tx_threshold为门限
    }
```

16.OperationPayCoin

该数据结构用于发送coin。

```text
    message OperationPayCoin{
        string dest_address = 1;                 // 目标账户地址
        int64 amount = 2;                        // coin的数量
        string input = 3;                        // 合约入参
    }
```

17.OperationLog数据结构

该数据结构用于记录log信息。

```text
    message OperationLog{
        string topic = 1;                        // 日志主题
        repeated string datas = 2;               // 日志内容
    }
```

18.OperationSetPrivilege数据结构

该数据结构用于设置账户权限。

```text
    message OperationSetPrivilege{
        string master_weight = 1;                            // 账户自身权重
        repeated Signer signers = 2;                         // 签名者权重列表
        string tx_threshold = 3;                             // 交易门限
        repeated OperationTypeThreshold type_thresholds = 4; // 指定操作的交易门限列表，未指定的操作的交易以tx_threshold为门限
    }
```

