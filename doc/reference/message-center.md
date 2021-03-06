# 消息中心需求分析&系统设计

## 一、需求原型分析
### 消息类型
 1. **通知**－－发送者通常为`应用本身`即超级用户，`实时推送`
    * 系统站内通知（版本升级、系统相关）
    * 系统邮件&短信推送，如某某课程要开学啦（选择某一推送规则（如立即发送），可定制）
    * 系统消息推送，如注册信息、手机验证码等（实时发送，不可定制）
 2. **提醒**（消息,`实时推送`）
    * 谁关注我 -- （站内&邮件，邮件推送可定制）
    * 谁邀请我 -- （站内&邮件，邮件推送可定制）
    * 谁喜欢了我发布的文章 -- （站内，无需定制）
    * 谁评论了我发布的文章 -- （站内&邮件，邮件推送可定制）
    * 谁评论了我评论过、关注过的文章 -- （站内&邮件，邮件推送可定制）
    * 谁不喜欢我发布的文章 -- （站内&邮件，邮件推送可定制）
    * 我关注的人发布新动态了 -- （站内&邮件，邮件推送可定制）
    * 我解答了红包任务获得奖金 -- （站内&邮件，邮件推送可定制）
    * 谁赞了我 （站内，可定制）
    * **加入众包项目 -- (站内&邮件，不可定制)**
    * **众包项目新增\编辑\评论Story -- (站内&邮件，不可定制)**
    * **众包项目状态更新 -- (站内&邮件，不可定制)**
    * **众包项目分配缺陷 -- (站内&邮件，不可定制)**
    * 会员等级晋升 -- （站内&邮件，邮件推送可定制）
    * 积分变动 -- （站内&邮件，邮件推送可定制）
 3. **站内私信**（`实时推送`）
    * 即时通讯webIM
 4. **订阅消息**（`策略推送`）
    * 系统推送(系统根据用户行为习惯分析，选择某一推送规则（如每周）推送，可定制)
    * 用户订阅(用户勾选感兴趣专题，选择某一推送规则（如每周）推送，可定制)

### 推送类型
 * 邮件
 * 短信
 * 站内消息

### 推送策略
 * 实时（立即发送）
 * 每周期（如每天、每周、每月等等）


## 系统设计
### 表设计
#### 站内消息表 [ `t_notify_app` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id              | 自增序号    |
| type            | 消息类型[1通知|2提醒|3私信|4推送] |
| content         | 消息体     |
| sender          | 发送者ID   |
| action          | 动作      |
| targetId        | 目标id    |
| targetType      | 目标类型(文章｜私信｜通知｜众包项目|红包任务） |
| targetOwner     | 目标属主    |
| createTime      | 创建时间 timestamp |

#### 私信消息表 [ `t_notify_message` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id              | 自增序号    |
| type            | 消息类型[3私信] |
| content         | 消息体     |
| sender          | 发送者ID   |
| action          | 动作      |
| targetId        | 目标id    |
| targetType      | 目标类型(文章｜私信｜通知｜众包项目|红包任务） |
| targetOwner     | 目标属主    |
| userId          | 消息用户id |
| receiver        | 接收者[用户在app内的id]     |
| isRead          | 是否已读 1已读 0未读 |
| isSend          | 是否发送 1已发送 0未发送 2发送失败 |
| createTime      | 创建时间 timestamp |

#### 邮件消息表 [ `t_notify_email` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id              | 自增序号    |
| type            | 消息类型[1通知|2提醒|4推送] |
| content         | 消息体     |
| sender          | 发送者ID   |
| action          | 动作      |
| targetId        | 目标id    |
| targetType      | 目标类型(文章｜私信｜通知｜众包项目|红包任务） |
| targetOwner     | 目标属主    |
| userId          | 消息用户id |
| receiver        | 接收者[通常是邮箱]     |
| pushStrategy    | 推送策略，实时or周期 |
| isRead          | 是否已读 1已读 0未读 |
| isSend          | 是否发送 1已发送 0未发送 2发送失败 |
| createTime      | 创建时间 timestamp |

#### 短信消息表 [ `t_notify_mobile` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id              | 自增序号    |
| type            | 消息类型[1通知|2提醒|4推送] |
| content         | 消息体     |
| sender          | 发送者ID   |
| action          | 动作      |
| targetId        | 目标id    |
| targetType      | 目标类型(文章｜私信｜通知｜众包项目|红包任务） |
| targetOwner     | 目标属主    |
| userId          | 消息用户id |
| receiver        | 接收者[通常是手机号]     |
| pushStrategy    | 推送策略，实时or周期 |
| isRead          | 是否已读 1已读 0未读 |
| isSend          | 是否发送 1已发送 0未发送 2发送失败 |
| createTime      | 创建时间 timestamp |



#### 用户消息表 [ `t_user_notify` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id              | 自增序号 |
| userId          | 消息用户id |
| receiver        | 接收者     |
| notifyId        | 消息id |
| isRead          | 是否已读 1已读 0未读 |
| isSend          | 是否发送 1已发送 0未发送 2发送失败 |
| createTime      | 创建时间 timestamp |

#### 订阅表 [ `t_subscription` ]
| 字段         | 描述           |
| ----------  | -------------- |
| id          | 自增序号        |
| userId      | 用户ID         |
| notifyType  | 消息类型        |
| targetId    | 目标ID         |
| targetType  | 目标类型        |
| actions     | 订阅动作[like,comment...] |
| tags        | 订阅标签，如我关注java、c、javascript相关内容，`我觉得后面再考虑用户行为分析的，第一期暂时不加` |
| range       | 订阅范围，属于那个app或模块的消息，`我觉得后面再考虑用户行为分析的，第一期暂时不加` |

#### 订阅配置表 [ `t_subscription_config` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id          | 自增序号        |
| userId      | 用户id         |
| config      | 订阅配置：0不订阅｜1订阅；如：{like:1,comment:1,hate:0,follow:1} |

#### 消息模版 [ `t_notify_template` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id         | 自增序号         |
| template | 消息模版（根据不同的动作，套用不同的模版，产生消息内容；比如点赞一个文章的时候，`谁` **点赞**了 我发布的 `《关于如何自由...》`） |

#### 推送类型表 [ `t_notify_channel` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id         | 自增序号         |
| channel | 推送渠道：0 站内 1 邮件 2 短信 |


#### 推送策略表 [ `t_notify_rule` ]
| 字段        | 描述           |
| ---------- | -------------- |
| id         | 自增序号 |
| rule       | 推送规则：0 实时 1 每天 2 每周 |


### 详细设计
 ...敬请期待...
