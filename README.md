# Ordinals Introduction

## 1 Ordinals（序数）

https://docs.ordinals.com/zh/introduction.html

由于比特币的 UTXO 模型的特性，所有的的比特币都可以通过 UTXO 来追溯到 coinbase 交易。而由于所有的 coinbase 都是所在区块的第一笔交易，而且一个区块只有一个 coinbase 交易。因此所有的比特币都可以按照区块的顺序进行排序，这个排序就是 Ordinals。

![](./imgs/8.png)

同时，由于 1 BTC 等于 100000000 satoshis，所以按照这个理论所有的 satoshis 都有了一个编号称之为序号。

序号有几种不同的表示方式：

* 整数符号:2099994106992659 这个序号是根据挖掘聪的顺序分配。

* 十进制符号: 3891094.16797 第一个数字是挖掘聪的区块高度，第二个数字是区块内聪的偏移量。

* 度数符号: 3°111094′214″16797‴。

```
A°B′C″D‴
│ │ │ ╰─ Index of sat in the block
│ │ ╰─── Index of block in difficulty adjustment period
│ ╰───── Index of block in halving epoch
╰─────── Cycle, numbered starting from 0
```

* 百分数: 99.99971949060254% . 以百分比表示聪在比特币供应中的位置

* 名字: satoshi. 一种使用字母 a 到 z对序号进行编码的方法，举个例子, 1905530482684727°'的名字是 "iaiufjszmoba"。

### 1.1 Ordinals 的转移

当每个聪都被编号以后，每个聪其实就可以看成是独一无二的了。但是在交易的时候，我们通常是将一定数量的聪进行转移，而不是一个一个的转移。这个时候就需要确定一个规范的转移方式。

在 Ordinals 协议中，规定了 first in first out 的转移方式。 

![](./imgs/9.png)

如上图所示，这是一个交易的输入和输出。 

输入有以下三个：

1. 2 聪，序号为 1 和 2
2. 1 聪，序号为 3
3. 1 聪，序号为 4

输出有以下两个：

1. 3 聪，序号为 1、2、3
2. 1 聪，序号为 4

我们可以看到，输出的聪的序号是按照输入的顺序进行排列的。这样就保证了每个聪的转移都是有序的。

当然由于粉尘聪的限制，真实的交易中每个输入和输出都可能有多个聪，但是这个规则是一样的。

## 2 Inscriptions（铭文）

Ordinals 协议有很多应用，比如稀有聪，稀有的名字等等。这些都是通过 Ordinals 协议来实现的。不过最近 Ordinals 协议的最大的应用就是铭文。

铭文是一种将数据铭刻到比特币区块链上的方法。铭文的数据可以是任意的，可以是文本、图片、视频等等。

简单来说就是在比特币脚本中嵌入数据，然后将这个脚本锁定在一个 UTXO 中，如果铭文数据中没有指针字段，那么这些数据被铭刻到了这个 UTXO 中的第一个聪之中。然后就可以根据序数理论来跟踪这个聪。当这个聪进行转移、购买等操作的时候，铭文数据也会随之转移。

## 3 铭文的应用

比较流行的铭文应用有两个，一个是 NFT，另一个是 BRC20。

![](./imgs/10.png)

### 3.1 NFT

其实从本质上来说所有铭刻的内容都可以看成是 NFT，比如纯文本的铭文，也可以看成是一个文本 NFT。不过在更狭义的定义中，提到比特币的 NFT 的时候，通常指的是图片。

### 3.2 BRC20

BRC20 铭刻的是一种特定格式的字符串，这个字符串包含了一些特定的信息，比如合约地址、发行总量、名称、符号等等。这些信息可以用来表示一个 BRC20 代币。

根据功能的不同，BRC20 的铭刻内容可以分为以下几种：

#### 1. 发行

```
{ 
  "p": "brc-20",
  "op": "deploy",
  "tick": "ordi",
  "max": "21000000",
  "lim": "1000"
}
```

#### 2. 铸造

```
{ 
  "p": "brc-20",
  "op": "mint",
  "tick": "ordi",
  "amt": "100"
}
```

#### 3. 转账

```
{ 
  "p": "brc-20",
  "op": "transfer",
  "tick": "ordi",
  "amt": "100"
}
```

#### 4. 流程

有一点需要注意的是，这些铭刻是没有限制的，任何人任何时候都可以进行铭刻。如果两个人 deploy 了同样的 BRC20 代币，只从比特币交易的层面来看，这两个是完全一样的。

因此需要一个索引器来对这些铭刻的行为进行索引。 索引器会按照一定的规则对所有的铭刻的交易进行索引。

这个规则可以是这样的：

1. 代币的名称是唯一的，如果有两个代币的名称相同，那么只有最早的一个会被索引，后面的都会被忽略。
2. 根据 mint 的铭刻计算某个地址的余额。
3. 根据 transfer 的铭刻计算某个地址的转账记录。如果没有 mint 就进行 transfer 的话，那么这个铭刻交易会被忽略。
4. ...

这些规则可能会有很多，而且每一个索引器的规则都可能不一样。目前最流行的是 Unisat 和 OKX 的这两个中心化的索引器。也有一些去中心化的索引器正在创建。

下面是一个大致的流程：

![](./imgs/11.png)

## 4 铭刻 

铭刻是指将数据铭刻到比特币区块链上的行为。

铭文内容完全在链上，存储在 taproot script-path spend脚本中。 Taproot 脚本对其内容的限制很少，并且额外获得见证折扣，使得铭文内容存储相对经济。

由于 taproot script-path spend 脚本只能从现有的 taproot 输出中产生，因此使用两阶段 commit/reveal 过程进行铭刻。首先，在commit中，创建一个提交到包含铭文内容的脚本的 taproot 输出。 其次，在 reveal 交易中，使用 commit 交易产生的输出，来显示链上的铭文内容。

## 5 铭文规范

随着协议的不断发展，铭文支持了越来越多的功能，这些功能对应着不同的规范。我们在下面的章节中会详细介绍这些规范。

### 5.1 铭文内容

#### 5.1.1 基本格式

铭文内容是基于万维网标准的。铭文由内容类型（也称为 MIME 类型）和内容本身（字节串）组成。

比如包含字符串“Hello, world!”的文本铭文序列化后的内容如下：

```
OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 1
  OP_PUSH "text/plain;charset=utf-8"
  OP_PUSH 0
  OP_PUSH "Hello, world!"
OP_ENDIF
```

外围的 `OP_FALSE OP_IF … OP_ENDIF` 称为信封，从操作符本身的含义来说这段是无意义的，这段逻辑不会被脚本执行。但是通过这种方式将铭文内容的数据保存到了比特币区块信息中。

`OP_PUSH "ord"` 表示序数协议。

`OP_PUSH 1` 表示后面内容是数据的 MIME 类型。

`OP_PUSH 0` 表示后续数据推送包含内容本身。对于大型铭文必须使用多个片段拼接。这是因为 taproot 的少数限制之一是单个数据推送不得大于 520 字节。

#### 5.1.2 内容说明

铭文的数据是以一种标准的 Web 格式存在，可以通过常规的 Web 技术进行传输和查看。这使得铭文的内容可以轻松地集成到现有的 Web 生态系统中，并且可以被广泛地访问和使用。

而且铭文的数据内容除了文字、图片、视频等类型外，还可以是 HTML、CSS、JavaScript 等代码数据。

#### 5.1.3 沙盒化

HTML 和 SVG 的铭文被沙盒化，以防止引用链下内容，从而保持铭文不可变和独立。

也就是说如果铭文的内容是 HTML 和 SVG 的时候，这些文件是在一个沙盒中执行的，不会也无法引用外部的资源。

同时铭文浏览器在渲染这些内容的时候会通过设置 iframe 标签的 sandbox 属性以及铭文内容资源的 Content-Security-Policy 响应头来实现沙盒化。

一个铭文引用另外的铭文（也就是递归铭文）这种情况是唯一的一个例外，这种情况下，被引用的铭文的内容是可以被访问的。

当铭文的内容是 HTML 时，如果该 HTML 引用了其他铭文，当铭文浏览器进行显示的时候，可以引用由中心化服务器提供的该铭文引用的铭文内容，然后将所有的内容统一进行渲染，显示给用户。

#### 5.1.4 字段（Fields）

铭文可以在正文之前包含一些字段。每个字段有两个数据：一个标签和一个值。

比如上面的例子中的 

```
OP_PUSH 1
OP_PUSH "text/plain;charset=utf-8"
```

就是一个字段，标签是 `1`，值是`text/plain;charset=utf-8`。

这表示了铭文的内容类型是 `text/plain;charset=utf-8`。

目前支持的字段有：

1. **`content_type`** ，标记为 `1` ，其值为正文的 MIME 类型。
2. **`pointer`** ，标签为 `2` ，用于指定铭刻的聪的位置。
3. **`parent`** ，标签为 `3` ，用于标识父铭文。
4. **`metadata`** ，标签为 `5` ，指定铭文的元数据信息，比如铭文的属性，集合的名称等等内容。
5. `metaprotocol` ，标记为 `7` ，其值为元协议标识符。
6. `content_encoding`, 标记为 `9` ，其值为正文的编码方式。
7. **`delegate`** ，标记为 `11` ，可以通过代理其他的铭文来引用其他铭文的信息，从而廉价的创建铭文副本。

我们在后面会详细介绍各种字段的具体用法，这里只是简单的介绍一下，先有个印象即可。

#### 5.1.5 Inscription ID

上面我们提到了铭文的铭刻过程中会有两个阶段，一个是 commit 阶段，一个是 reveal 阶段。在 commit 阶段，铭文的内容是不可见的，只有在 reveal 阶段才会被公开。因此铭文的具体内容包含在揭示交易的输入中。为了唯一地标识它们，它们被分配了一个 ID，其形式如下：

```
521f8eccffa4c41a3a7728dd012ea5a4a02feed81f41159231251ecf1e5c79dai0
```

这里以字母 `i` 为分隔符，分成两部分。

`i` 前面的部分是 reveal 交易的ID （ txid ）。后面 `i` 的数字定义了在 reveal 交易中铭刻的新铭文的索引（从 0 开始的十进制数据）。

铭文可以位于不同的输入中，也可以位于同一输入中，也可以位于两者的组合中。在任何情况下，排序都是明确的，因为解析器将连续遍历输入并查找所有铭文 envelopes 。

| Input | Inscription Count | Indices |
|-------|------------------|---------|
| 0     | 2                | i0, i1  |
| 1     | 1                | i2      |
| 2     | 3                | i3, i4, i5 |
| 3     | 0                |         |
| 4     | 1                | i6      |

上面这个表中表示了一个交易中的铭文的分布情况。比如第一个输入中有两个铭文，分别是 `i0` 和 `i1`。第二个输入中有一个铭文，是 `i2`。第三个输入中有三个铭文，分别是 `i3`、`i4` 和 `i5`。第四个输入中没有铭文。第五个输入中有一个铭文，是 `i6`。

#### 5.1.6 Inscription Mumbers

每个铭文都被分配了一个从零开始的铭文编号，编号是按照交易顺序以及envelopes顺序来进行顺序分配的。

被诅咒的铭文从负一开始编号，倒计时。在第 824544 块之后被诅咒的铭文被证明是正确的，并被分配了正的铭文编号。

#### 5.1.7 Self-Reference 

上面我们提到了 HTML 和 SVG 类型的铭文可以通过引用其他铭文来引用其他铭文的信息。这种引用是通过 `/content/<INSCRIPTION_ID>` 这种方式来引用的。

同样的，铭文也可以引用自己。这种引用是通过下面的方式来实现的

```
let inscription_id = window.location.pathname.split("/").pop();
```

对于委托铭文来说：如果ID 为 X 的铭文包含了 ID 为 Y 的铭文的委托字段，则铭文 X 的内容必须从 URL 路径 `/content/X` 提供，而不是 `/content/Y` 。

下面的例子是 ID 为 X 的铭文的内容，其中包含了 ID 为 Y（0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a09080706050403020100） 的铭文的委托字段。 
```
OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 11
  OP_PUSH 0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a09080706050403020100
OP_ENDIF
```

#### 5.1.8 Reinscriptions 重新铭刻

如果一个聪上面已经有了一个铭文，可以重新铭刻新的内容到之前铭文的聪上面。这只会将新的铭文附加到 sat 上，而不会更改初始铭文。

### 5.2 Pointer（指针）

为了在第一个输入之外的 sat 上进行铭文，可以通过提供一个从零开始的整数：“指针”来实现。对应的标签为 `2` 。

如果指针等于或大于刻写事务输出中的总 sat 数，则忽略它，并照常进行刻写。指针字段的值是一个**小端字节序**的整数，尾随零将被忽略。

这可用于单个交易中在不同聪上的创建多个铭文。

**注意**：如果不指定指针。则同一个交易中的同一个输入中的铭文会被铭刻在同一个聪上，这个聪就是该输入中的第一个聪。同样的其他输入中的铭文会被铭刻在对应输入的第一个聪上面。

具体的可以看下面的例子。

#### 5.2.1 指针的使用

```
OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 1
  OP_PUSH "text/plain;charset=utf-8"
  OP_PUSH 2
  OP_PUSH 0x0001
  OP_PUSH 0
  OP_PUSH "Hello, world!"
OP_ENDIF
```

上面这个例子中，指针的值是 `0x0001`，这个值是一个小端字节序的数，尾随零将被忽略。这个指针的值是 `256`。这将导致铭文被铭刻在第 256 个聪上。

### 5.3 Provenance（归属）

铭文的所有者可以创建子铭文，子铭文中包含了父铭文的 ID。这样就可以通过子铭文来查找父铭文。也能保证子铭文是由父铭文的所有者创建的。这可用于集合，父铭文的子项是同一集合的成员。

子铭文还可以有自己的子铭文，可以形成复杂的层次结构。

#### 5.3.1 父子铭文的使用规范

这里我们以一个简单的例子来说明父子铭文的使用规范。

我们现在有一个铭文 P，然后我们要创建他的子铭文 C。我们可以通过下面的方式来创建子铭文 C。

1. 为 C 的铭刻创建一个交易 
2. 将 P 作为这个交易的一个输入
3. 在 C 的铭刻数据中添加一个 `parent` 字段，也就是 `OP_PUSH 3`，然后将 P 的 ID 中的交易哈希序列化为 32 字节的小端字节序整数，添加到字段中去。然后将 P 的 ID 中后面 index 数据也转化为 4 字节的小端字节序整数，省略尾随零，然后添加到字段中去。

注意：比特币交易 ID 的字节在其文本表示中是相反的，因此序列化的交易 ID 将采用相反的字节顺序。

#### 5.3.2 父子铭文的使用示例

现在有一个铭文 P，其 ID 为 `000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1fi256`。现在我们要创建一个子铭文 C。

```
OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 1
  OP_PUSH "text/plain;charset=utf-8"
  OP_PUSH 3
  OP_PUSH 0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a090807060504030201000001
  OP_PUSH 0
  OP_PUSH "Hello, world!"
OP_ENDIF
```

这里面需要注意的就是父铭文的 ID 的序列化。

父铭文的 ID 首先是交易 ID，然后是 index。交易 ID 是 `000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f`，index 是 `256`。

首先对交易 ID 进行反转，得到 `0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a09080706050403020100`。

然后 index  `256` 转换为十六进制为 `100`，将其补齐后是`0100`，然后进行翻转得到`0001`，没有尾随零因此最终结果是`0001` 。

最后得到的结果是 `0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a090807060504030201000001`。

另外的一个例子，如果父铭文的 ID 是 `000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1fi0`，其中 index 是 `0`。

结果是

```
OP_FALSE
OP_IF
  …
  OP_PUSH 3
  OP_PUSH 0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a09080706050403020100
  …
OP_ENDIF
```

### 5.4 Metadata（元数据）

元数据字段用于存储铭文的元数据信息，比如铭文的属性，集合的名称等等内容。这个字段的标签是 `5`。

元数据字段的值是一个 CBOR 对象，这个对象包含了铭文的元数据信息。

CBOR 可以看成是 JSON 的二进制版本，它是一种轻量级的数据交换格式，类似于 JSON。CBOR 是一种二进制格式，它的编码和解码速度都比 JSON 快，而且编码后的数据量更小。而且 CBOR 支持的数据类型更多，比如二进制数据、日期时间等等。

与铭文内容一样，当元数据字段的值超过 520 字节时，必须使用多个片段拼接。

ord 官方推荐将元数据呈现为 HTML 以进行显示。但是大部分铭文浏览器都是直接将其转化为 JSON 格式或者直接限制出具体内容。

### 5.5 Delegate（代理）

代理字段用于通过代理其他的铭文来引用其他铭文的信息，从而廉价的创建铭文副本。这个字段的标签是 `11`。

#### 5.5.1 代理字段的使用规范

现在已经有了一个铭文 D，我们要创建一个铭文 I。I 的内容是 D 的内容的副本。我们可以通过下面的方式来创建铭文 I。

在 I 的铭刻数据中添加一个 `delegate` 字段，也就是 `OP_PUSH 11`，然后将 D 的 ID 中的交易哈希序列化为 32 字节的小端字节序整数，添加到字段中去。然后将 D 的 ID 中后面 index 数据也转化为 4 字节的小端字节序整数，省略尾随零，然后添加到字段中去。

需要注意的是当铭文 I 被创建的时候，铭文 D 不是必须已经存在的。如果不存在的时候，对铭文 I 的查询会返回一个 404 的结果。

#### 5.5.2 代理字段的使用示例

现在有一个铭文 D，其 ID 为 `000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1fi0`。现在我们要创建一个铭文 I。

```
OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 11
  OP_PUSH 0x1f1e1d1c1b1a191817161514131211100f0e0d0c0b0a09080706050403020100
OP_ENDIF
```

### 5.6 Recursion（递归）

铭文可以引用其他铭文或者区块高度等信息。这种就称之为递归引用。

这种递归的引用是通过递归端点（Recursive endpoints）方式来引用的。

#### 5.6.1 递归端点的使用规范

递归端点包括以下几种：

1. `/content/<INSCRIPTION_ID>` ：铭文 `<INSCRIPTION_ID>` 的内容
2. `/r/blockhash/<HEIGHT>`: 给定区块高度的区块哈希。
3. `/r/blockhash`: 最新的区块哈希。
4. `/r/blockheight`: 最新的区块高度。
5. `/r/blockinfo/<QUERY>`: 区块信息. `<QUERY>` 可以是区块高度或者区块哈希.
6. `/r/blocktime`: 最新的区块时间.
7. `/r/children/<INSCRIPTION_ID>`: 某个铭文的前 100 个子铭文。
8. `/r/children/<INSCRIPTION_ID>/<PAGE>`: 获取子铭文的第 `PAGE` 页的 100 个子铭文。
9. `/r/inscription/<INSCRIPTION_ID>`: 铭文的信息
10. `/r/metadata/<INSCRIPTION_ID>`: 包含十六进制编码的 CBOR 元数据的 JSON 字符串
11. `/r/sat/<SAT_NUMBER>`: 某个聪上面的前 100 个铭文。
12. `/r/sat/<SAT_NUMBER>/<PAGE>`: 获取聪上面的第 `PAGE` 页的 100 个铭文。
13. `/r/sat/<SAT_NUMBER>/at/<INDEX>`: 某个聪上的第 `INDEX` 个铭文。`INDEX` 为 0 表示第一个铭文。-1 代表最后生成的铭文。

注意： `<SAT_NUMBER>` 只允许 sat 的实际数字，不允许其他 sat 符号，如度数、百分位数或十进制。

来自上述递归端点的响应是 JSON。为了向后兼容，支持其他端点，其中一些返回纯文本响应。

比如 `/blockheight` `/blockhash` `/blockhash/<HEIGHT> ` `/blocktime` 这些端点返回的是纯文本响应。

由于递归端点的更改可能会破坏依赖他们的铭文，因此递归端点具有以下特性：

1. 递归端点不会被删除
2. 递归端点防火的对象的字段不会被重命名或者更改类型。

## 6 铭文的索引

铭文的索引是一个用来对铭文的内容进行索引的工具。铭文的内容是存储在比特币区块链上的，而区块链的数据是不可变的，因此铭文的内容也是不可变的。但是铭文的内容是可以被索引的，这样就可以通过索引来查找铭文的内容。

## 7 铭文实例

### 7.1 Inscription ID

https://mempool.space/signet/tx/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1

这个交易中有 4 个输入，并且每个输入都有一定数量的铭文。

其 ID 分别如下所示。

| Input | Inscription Count | Indices |
|-------|------------------|---------|
| 0     | 2                | [i0](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i0), [i1](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i1)  |
| 1     | 1                | [i2](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i2)      |
| 2     | 3                | [i3](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i3), [i4](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i4), [i5](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i5) |
| 3     | 4                | [i6](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i6), [i7](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i7), [i8](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i8), [i9](https://signet.ordinals.com/inscription/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1i9) |

上面的这个交易中有 4 个输入，并且每个输入都有一定数量的铭文，铭文中没有指针。

该交易只有一个输出：https://signet.ordinals.com/output/1688b64f86f7af4f66d45225f336d24145a64ff5e45aa8b7f0fdc002f403a1d1:0

输出中的所有的聪有以下的范围
* 572367169552182 – 572367169553509
* 572367169553509 – 572367169554832
* 572367169554832 – 572367169556162
* 572367169556162 – 572367169556317

这些对应着上面的四个输入的聪的范围（最后一个范围略小于其对应的输入）。

每一个输入中的铭文都被铭刻在其输入对应的首个聪上。

* `i0`，`i1` 铭文被铭刻在 572367169552182 这个聪上。
* `i2` 铭文被铭刻在 572367169553509 这个聪上。
* `i3`，`i4`，`i5` 铭文被铭刻在 572367169554832 这个聪上。
* `i6`，`i7`，`i8`，`i9` 铭文被铭刻在 572367169556162 这个聪上。

注意：如果输出中聪的总数量小于输入的数量，而且没有使用指针的话，铭刻的铭文会出现丢失的情况。

比如下面的交易：

https://signet.ordinals.com/tx/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408

其中铭文 ID 分别如下所示。

| Input | Inscription Count | Indices |
|-------|------------------|---------|
| 0     | 2                | [i0](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i0), [i1](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i1)  |
| 1     | 1                | [i2](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i2)      |
| 2     | 3                | [i3](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i3), [i4](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i4), [i5](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i5) |
| 3     | 4                | [i6](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i6), [i7](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i7), [i8](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i8), [i9](https://signet.ordinals.com/inscription/29be30fefaa15aeee3bc0c0732d6bef19e4d6c7075eb4fe7c71a6877e06b1408i9) |

这个交易中有四个输入，也是只有一个输出。

但是输出中的聪的范围是：

* 566366352176691–566366352178018
* 566366352178018–566366352179091

这个范围小于输入的数量，而且没有使用指针，所以铭文会出现丢失的情况。

比如 `i0` 和 `i1` 铭文会被铭刻在 566366352176691 这个聪上，而 `i2` 铭文会被铭刻在 566366352178018 这个聪上。而 `i3，i4，i5` 这些铭文会被铭刻在其他输出的聪上（在这个交易中是给矿工的手续费中）。

### 7.2 Pointer

为了防止铭刻出现上面的情况就需要使用指针。

比如下面的交易：

https://signet.ordinals.com/tx/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efa

其中铭文 ID 分别如下所示。

| Input | Inscription Count | Indices |
|-------|------------------|---------|
| 0     | 2                | [i0](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai0), [i1](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai1)  |
| 1     | 1                | [i2](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai2)      |
| 2     | 3                | [i3](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai3), [i4](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai4), [i5](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai5) |
| 3     | 4                | [i6](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai6), [i7](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai7), [i8](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai8), [i9](https://signet.ordinals.com/inscription/443c8580e29f8c5f8e8119d9367485418775fec469c98b3d33df319cd29e6efai9) |

这个交易中有四个输入，也是只有一个输出。

但是通过指针的使用，铭文都被铭刻在了输出的第一个聪上面。

### 7.3 Provenance 

**注意**：父铭文铭刻的聪必须是在子铭文铭刻的揭示交易的 input 中。

换句话说就是父铭文必须作为子铭文铭刻的交易的一个输入。

父铭文

https://signet.ordinals.com/inscription/3f262da28d8ab6b3a19be31ac7f215cd0b24c030f270353d16cf89bf51acfb4fi0

子铭文

https://signet.ordinals.com/inscription/cb3bb3bab5e8348ea7dced808e9fc85e98d9072c6cca94060ad2e56a5e3ea49fi0

https://signet.ordinals.com/inscription/cb3bb3bab5e8348ea7dced808e9fc85e98d9072c6cca94060ad2e56a5e3ea49fi1

### 7.4 Delegate

下面第一个是被代理铭文 D，第二个是代理铭文 I。

https://signet.ordinals.com/inscription/0cf3a6e110e7e76271662f7d6d05a1fd3214153eef182797161aee1e9a3f91c9i0

https://signet.ordinals.com/inscription/94ca74cd54bacddfb6d1748907838995677191175a90400078c46d481580ef53i0

查看内容会发现 I 中显示的是 D 中的内容。

另外一个例子

被代理铭文 D

https://signet.ordinals.com/inscription/babfc280e0e38b0898f37bf9f087cd2a5c5aa470b8af8007ea8b2dca889d84f9i256

代理铭文 I

https://signet.ordinals.com/inscription/e27884e551d884cac3a23b82d0250795dd461d77f94cd1c800a45df99a9ecdb8i0

### 7.5 Metadata

https://signet.ordinals.com/inscription/8482ea175cec55fab048b81cfd3adeb57e6431a37457e7a59b55e4e6030a6d6fi0

铭文的 metadata 内容转换成 json 如下所示：

```
{
  "integer": 123,
  "float": 123.456,
  "string": "hello world",
  "array": [1, 2, 3],
  "object": {
    "key1": "value1",
    "key2": "value2"
  },
  "boolean": true,
  "null": null,
  "undefined": "undefined",
  "byteString": "48656c6c6f20576f726c64",
  "taggedData": {
    "tag": 1,
    "value": "2022-12-31T23:59:59Z"
  }
}
```

### 7.6 Recursion（递归）



## Runes（符文）

