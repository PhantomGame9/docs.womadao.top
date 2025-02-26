# 机器人“店员”使用文档

## 更新日志：
- 2024.07.10 
    - 更新了[扫雷](#扫雷小游戏)小游戏
    - 更新了[游戏玩家队列系统](#游戏玩家队列系统)
    - 更新了[防刷屏](#基本功能特性)
- 2024.07.11
    - 更新了[自动宵禁](#基本功能特性)

## 一、介绍

- **开发者**：[橙息](https://space.bilibili.com/112978436)
- **设定**：来自b站up主[@warma](https://space.bilibili.com/53456)的作品中出现的角色“店员”
- **功能**：基于deepseek等AI模型的聊天机器人，可以同时与**多人**进行**连续**的文字聊天，识别图像，调用外部函数等。

### 使用到的AI模型
- **语言模型：**[DeepSeek-V2](https://deepseek.com/)
- **图像识别：**[qwen-vl-plus](https://tongyi.aliyun.com/qianwen/)(通义千问视觉模型)

>以下内容中以`频道`指代聊群或私信频道

### 基本功能特性
1. **白名单**：需要橙息手动为频道添加白名单才能使用店员的所有功能
2. **闭嘴**：当店员处于闭嘴状态的时候，无法在频道中通过 `@店员` 除外的方式触发AI回复
3. **防刷屏**：主要是为了防止店员与其他机器人(贝拉)触发循环对话
    - 判定方式：在触发店员AI对话的时候，判断最近5条消息是否包含2条店员消息，并且5条消息的时间间隔小于8秒，如果是则不触发AI回复
4. **自动宵禁**：当自动宵禁功能开启，工作日00:00-06:30自动开启全员禁言

## 二、店员AI聊天功能

### **触发AI对话的方式**：
- 在聊群中发送带有“**店员**”二字的消息
- 在聊群中 `@店员` 
- **引用**店员的消息
- 在私信中发送**任意**消息
- 在聊群中有 **1%** 的概率触发店员的AI回复
- 若消息中包含`**`（双星号）则任何情况下都**不会**触发AI回复
- 有新用户**加入聊群**，自动触发AI回复欢迎新用户
### **记忆**
- 在每个频道都会有**20**条对话上下文（又称短期记忆）
- 记忆包括用户的对话（没有触发AI回复的消息）
- 还包括**店员自己发送的消息**和**调用外部函数的结果**
- 超过**20**条后会自动删除最早的一条对话。
- 店员可以结合这**20**条消息来回答你的问题。
### **可用信息**
- 用户发送的**消息文本**
- 用户在聊群中的**昵称**（无法在私信中获取）
- 当前的**时间** （格式为：YYYY-mm-dd HH:MM:SS）
- **频道名称**
- 个别频道的**额外**信息
    - 闲聊频道：各个子频道群号列表
    - 沃玛岛：基础的帮助文档
### **店员接受的格式**
- **文本**内容
- **图片**：可以是**引用**的**图片**，又或是`图片`+`文字`混合（自定义表情包将作为图片处理）
    >图片会先传递给**qwen-vl-plus**模型进行识别，识别结果会作为**文字**代替文本消息中的**图片**传递给**deepseek-v2**模型，**deepseek-v2**模型会根据识别结果回复
- **qq emoji**：转为对应的**文字**名称
- **qq表情包**：转为对应的**文字**名称
- **~~语音~~**：语音识别，做了但是没部署，因为要显卡
- **引用消息**：可获取引用的消息**文本**内容和发送者的**昵称**，如果引用的消息包含**图片**也会按照上述方式处理
- **其他格式**：无法处理文件、视频等格式
### **店员可输出的格式**
- **文本**内容
- **图片**（使用图片的url来发送**图片**，前提是店员得知道一张图片的url）
- **~~语音~~**: 做了，很屎，要显卡，没部署。
- **Latex公式**：会自动渲染为**图片**，有时候会抽风
- **代码块**: 大部分时候都会将代码块渲染为**图片**
### **调用外部函数**
- **事件触发器**：可以定时提醒用户做某件事情。
- **网页搜索**：调用google搜索api
- **b站搜索**：在b站搜索视频/up主/专栏等
- **获取用户主页投稿信息**：投稿的视频列表
- **获取视频内容**：由b站AI生成的视频总结
- **获取视频数据**：标题/简介/up主/播放量/弹幕/评论/点赞/投币/收藏/转发等数据

## 三、店员指令功能

- 使用 “`@店员` ” <指令> <参数> 来执行指令（通常使用**空格**分隔）
- 只要消息以“**@店员** ”开头，就算没有@到，也会当做指令来处理而不是直接触发AI回复
- 当“`@店员` ”后**没有**匹配到如下列表的指令时，触发AI回复

### 指令列表

#### 权限介绍
- **所有人**：所有用户都能使用的指令
- **管理员**：指定的管理员列表中的用户才能使用的指令
- **op**：指定的op列表中的用户才能使用的指令
- **群主**：当前所在频道的群主
- **群管理员**：当前所在频道的管理员

| 指令名 | 参数 | 权限 | 介绍 | 示例 | 备注 |
| ------ | ------ | ------ | ------ | ------ | ------ |
| 绑定      | 玩家名 | 所有人 | 绑定MC服务器皮肤站 | `@店员`  绑定 cxchency | 增加了鲁棒性，“绑定”与“玩家名”之间不需要空格也能执行
| 管理员绑定 | 玩家名; `@用户` | op/管理员 | 管理员手动绑定 | `@店员`  管理员绑定 cxchency `@橙息`|
| 解绑 | 玩家名 | op/管理员 | 解除绑定 | `@店员`  解绑 cxchency |
| 绑定查询 | 玩家名 | op/管理员 | 查询玩家名对应的qq号 | `@店员`  绑定查询 cxchency |
| 绑定反查 | qq号 | op/管理员 | 查询qq号对应的玩家名 | `@店员`  绑定反查 123456789 |
| 在线玩家 | - | 所有人 | 查询沃玛岛服务器在线玩家列表 | `@店员`  在线玩家 |
| tps | - | 所有人 | 查看沃玛岛服务器tps | `@店员`  tps |
| [排行榜](#1-排行榜) | - | 所有人 | 查询沃玛岛服务器统计信息排行 | `@店员`  排行榜-<?页数> <统计分类> <?统计项> | [点击查看具体用法](#1-排行榜)|
| [分类排行榜](#2-分类排行榜) | - | 所有人 | 查询沃玛岛服务器统计信息的某个分类下的所有条目的统计排行 | `@店员`  分类排行榜-<?页数> <统计分类> <?玩家名> <升序/降序> | [点击查看具体用法](#2-分类排行榜) |
| [排名](#3-排名) | - | 所有人 | 查询指定玩家名在沃玛岛服务器统计信息中某个条目的排行名次 | `@店员`  排名 <玩家名> <统计分类> <?统计项> | [点击查看具体用法](#3-排名)
| 记忆清除 | - | 群主/群管理员/管理员 | 清除店员AI聊天在当前频道的短期记忆 | `@店员`  记忆清除 |
| 重启 | - | 管理员 | 重启店员主进程 | `@店员`  重启 |
| git | pull | 管理员 | 从仓库拉取最新代码并重启 | `@店员` git pull |
| [扫雷](#扫雷小游戏) | 开始/显示/结束/帮助 | 所有人 | 扫雷小游戏，[点击查看具体用法](#扫雷小游戏) | `@店员`  扫雷 开始 简单 | 具体命令请使用 "`@店员` 扫雷 帮助" 来查看 |
| 开始游戏 | 游戏名 | 所有人 | 在当前子频道启动一个玩家队列 | `@店员` 开始游戏 真心话大冒险 | [点击查看具体用法](#游戏玩家队列系统) |
| 结束游戏 | - | 所有人 | 在当前子频道结束一个玩家队列 | `@店员` 结束游戏 | [点击查看具体用法](#游戏玩家队列系统) |
| 加入游戏 | - | 所有人 | 加入当前子频道的游戏玩家队列 | `@店员` 加入游戏 | [点击查看具体用法](#游戏玩家队列系统) 
| 退出游戏 | - | 所有人 | 退出当前子频道的游戏玩家队列 | `@店员` 退出游戏 | [点击查看具体用法](#游戏玩家队列系统) |
| 下一个 | - | 所有人 | 查询当前子频道游戏玩家队列中的下一个玩家 | `@店员` 下一个 | [点击查看具体用法](#游戏玩家队列系统) 
| 自动宵禁 | 开启/关闭 | 管理员 | 开启/关闭自动宵禁功能 | `@店员` 自动宵禁 开启 | 

### 其他指令

#### **mc指令执行器**
- 在任意频道，沃玛岛MC服务器管理员(op)可以发送"//"开头的消息来执行任意mc指令
- 如使用`//list`来查看在线玩家列表。

#### **排行榜系统**
##### 1. 排行榜
- 查询沃玛岛服务器统计信息排行
- 用法：
    ```
    @店员 排行榜-<?页数> <统计分类> <?统计项>
    ```
- 统计分类有如下：
`丢弃` `破坏` `挖掘` `合成` `被杀` `拾取` `击杀` `使用` `其他`
- 除“其他”类型以外，对应的 **<统计项>** 为对应实体/方块/物品id
    > “其他”类型的 **<统计项>** 参考 [统计信息列表](https://minecraft.fandom.com/zh/wiki/统计信息?variant=zh&printable=yes#统计信息列表\n\n若不使用) 
- 若不使用 **<统计项>** 则统计**整个分类**的合计数
**使用示例：**
    ```    
    “@店员 排行榜 挖掘 diamond_ore” 将返回挖掘钻石矿的排行榜
    “@店员 排行榜-2 击杀 将返回合计击杀的排行榜的第二页
    ```

##### 2. 分类排行榜
- 查询沃玛岛服务器统计信息的某个分类下的所有条目的统计排行
- 用法:
    ```
    @店员 分类排行榜-<?页数> <统计分类> <?玩家名> <?升序/降序>
    ```
- 统计分类有如下：
`丢弃` `破坏` `挖掘` `合成` `被杀` `拾取` `击杀` `使用` `其他`
**使用示例：**
    ```
    “@店员 分类排行榜 挖掘” 将返回全服挖掘各类方块的排行榜
    “@店员 分类排行榜-2 击杀 cxchency” 将返橙息的击杀各类型生物的排行榜的第二页
    ```
##### 3. 排名
- 查询指定玩家名在沃玛岛服务器统计信息中某个条目的排行名次
- 用法:
    ```
    @店员 排名 <玩家名> <统计分类> <?统计项>
    ```
- 统计分类有如下：
`丢弃` `破坏` `挖掘` `合成` `被杀` `拾取` `击杀` `使用` `其他`
- 除“其他”类型以外，对应的 **<统计项>** 为对应实体/方块/物品id
    > “其他”类型的 **<统计项>** 参考 [统计信息列表](https://minecraft.fandom.com/zh/wiki/统计信息?variant=zh&printable=yes#统计信息列表\n\n若不使用) 
- 若不使用 **<统计项>** 则统计**整个分类**的合计数
- 用法:
    ```
    “@店员 cxchency 排名 挖掘 diamond_ore” 将返回橙息在全服挖掘钻石矿次数的名次
    “@店员 cxchency 排名 击杀” 将返回橙息合计击杀的名次
    ```

#### **扫雷小游戏**
1. 开始游戏：@店员 扫雷 **开始** [难度] (难度可选：简单/中级/高级)
2. 结束游戏：@店员 扫雷 **结束**
3. 显示游戏：@店员 扫雷 **显示**
4. 点开方块：.挖开/挖/d/D [坐标] (例如：".d a1", 或者 ".d a1-3" 来连续挖开多个方块)
5. 标记地雷：.标记/插旗/f/F [坐标] (例如：".f a1", 或者 ".f a1-3" 来连续标记多个方块)
6. 双键挖开：.双键/双击/m/M [坐标] (例如：".m a1")

#### **游戏玩家队列系统**

- 在当前子频道创建一个循环的游戏玩家队列
- 每个子频道只能创建一个队列
- 子频道之间的队列不互通
- 指令：
    - **开始游戏**
        - 别称：游戏开始
        - 参数：游戏名
        - 示例：`@店员` 开始游戏 真心话大冒险
    - **结束游戏**
        - 别称：游戏结束
        - 示例：`@店员` 游戏结束
    - **加入游戏**
        - 示例：`@店员` 加入游戏
    - **退出游戏**
        - 示例：`@店员` 退出游戏
    - **下一个**
        - 查询队列中的下一个玩家
        - 示例：`@店员` 下一个

## 四、微博动态推送

- 以`图片`+`文本`的形式自动推送微博[@_warma_](https://weibo.com/u/1782488734)的动态
- 将动态中的**图片**逐个提取并发送
- 如果动态包含**视频**，会自动发送**视频文件**
- 将微博**文本**内容转发给店员AI来生成回复

## 五、b站动态推送

- 以`图片`+`文本`的形式自动推送b站[@Warma](https://space.bilibili.com/53456)和[@warma养鸽场](https://space.bilibili.com/106320250)的动态/视频投稿
- 将动态中的**图片**逐个提取并发送
- 将动态**文本**内容转发给店员AI来生成回复

## 六、MC服务器消息互通

- 在q群[MC服务器消息互通](https://qm.qq.com/q/i3QRaRjcgS)实现**双向**消息互通
- 消息互通群中的店员AI系统**独立**于其他频道，是以游戏玩家身份加入MC服务器，并借助消息互通回答用户或执行命令