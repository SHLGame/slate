---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - solidity

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Kittn API
---

# Introduction

这里将会介绍Connexion所有合约的技术细节，其中包括FT、NFT、Market、Treasure和Auction几部分内容。

# 合约实现细节

## ERC20代币合约
 
 除了FT代币的基础功能以外，还需要提供一键创建代币合约的功能。为了实现这个功能，我们编写了3个合约，分别是：`GameERC20Factory.sol`、 `GameERC20Proxy.sol` 和 `GameERC20Token.sol`，下面是他们的关系图：

 ![image ERC20关系图](./images/ERC20.png)

 对 delegatecall 熟悉的人应该知道：合约A通过delegatecall调用合约B，交易会按照合约B的逻辑执行，但是执行的上下文和更改的状态都在合约A中，我们称合约A为代理合约，合约B为逻辑合约。`GameERC20Proxy`就是代理合约，存储token的状态，用户可以通过`GameERC20Factory`创建任意个`GameERC20Proxy`的实例，每个实例就是一个token，所有通过`GameERC20Factory`创建出来的token的逻辑合约都是`GameERC20Token`。

### GameERC20Factory

#### Storage variable

##### vaultCount

```solidity
uint256 public vaultCount;
```

 vaultCount表示已经创建的token总数

##### vaults

```solidity
mapping(uint256 => address) public vaults;
```

 vaults存储已经创建的token地址

##### logic

```solidity
address public immutable logic;
```

 logic是所有代币的逻辑合约地址，使用不可变的变量存储逻辑合约地址保证每个工厂创建出来的代币逻辑合约都是一样的。

#### Functions

##### generate

```solidity
function generate(
    string memory _name,
    string memory _symbol,
    uint256 _cap
) external whenNotPaused returns (uint256 _index) {
    bytes memory _initializationCallData =
    abi.encodeWithSignature(
        "initialize(string,string,uint256,address)",
        _name,
        _symbol,
        _cap,
        msg.sender
    );

    address vault = address(
        new GameErc20Proxy(
            logic,
            _initializationCallData
        )
    );

    vaults[vaultCount] = vault;
    vaultCount++;

    return vaultCount - 1;
}
```

generate是用来创建新代币的方法

Parameters:

Name | Type | Description
--------- | ------- | -----------
_name | string | token's name
_symbol | string | token's symbol
_cap | uint256 | 代币的最大容量

Return Values:

Name | Type | Description
--------- | ------- | -----------
_index | uint256 | token's maximum capacity

### GameErc20Proxy

#### Storage variable

##### logic

```solidity
address public immutable logic;
```

 logic是所有代币的逻辑合约地址，使用不可变的变量存储逻辑合约地址保证token的合约逻辑不可以更改，可升级的合约就是通过改变logic合约实现的。

#### Functions

##### fallback

```solidity
fallback() external payable {
    address _impl = logic;
    assembly {
        let ptr := mload(0x40)
        calldatacopy(ptr, 0, calldatasize())
        let result := delegatecall(gas(), _impl, ptr, calldatasize(), 0, 0)
        let size := returndatasize()
        returndatacopy(ptr, 0, size)
        switch result
            case 0 {
                revert(ptr, size)
            }
            default {
                return(ptr, size)
            }
    }
}
```

 


## 多游戏通用的 ERC721 token contract

## 金库合约

### ERC20 金库合约

### ERC721 金库合约

## 交易市场合约

## 荷兰拍卖合约

# 控件列表

## 右边代码：

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

## 注意提醒控件

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

## 表格

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

