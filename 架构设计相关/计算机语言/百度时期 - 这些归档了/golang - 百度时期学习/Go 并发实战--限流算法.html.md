---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-30 10:19:27 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战\--限流算法
updated: "2019-06-20 13:23:09 +0000"
---

:::  
高并发系统为了服务的可用性面对高流量及qps高峰时通常有三种常见的应对措施：缓存、降级和限流。这一篇我们来看一下限流及go相应的实现。\
限流算法通常有这么几种：计数器、令牌痛、漏桶，这几个算法的优缺点在这里就不多说了，网上有大量的文章介绍这几个算法，大家也可以借鉴我限流算法的那篇文章。\
这里就这几种算法的思想借助go的API来实现一下：

#### 计数器限流  

这里用到的并发相关的API
主要是`sync.Mutex` \
我们通过设定一个计数器ReqCount，当ReqCount大于MaxCount（计数器最大值）时不发放令牌（token），过来的请求因拿不到令牌直接返回或者触发其他的拒绝策略。\
因为计数器的操作是一个典型的先判断再操作的竞态条件，并且存在另外一个专门置0的go协程，如果不加锁，在并发场景下发生问题的概率是非常大的，所以使用sync.Mutex，保证了操作的原子性。\
计数器比较简单，下面来看一下实现的代码：

```  
type AbandonReqLimitService struct {
    Interval time.Duration // 设置计数器的时间间隔
    MaxCount int // 计数器的最大值
    Lock     sync.Mutex // 锁
    ReqCount int // 计数器
}

func CreateAbandonReqLimitService(interval time.Duration, maxCount int) *AbandonReqLimitService {
    reqLimit := &AbandonReqLimitService{
        Interval: interval,
        MaxCount: maxCount,
    }
    go func() { // 开启一个go协程来定时更新计数器
        ticker := time.NewTicker(interval) // go中的定时器
        for {
            <-ticker.C
            reqLimit.Lock.Lock()
            reqLimit.ReqCount = 0
            reqLimit.Lock.Unlock()
        }
    }()
    return reqLimit
}

func (reqLimit *AbandonReqLimitService) GetTokenAbandonRequest() bool { // 取令牌函数
    reqLimit.Lock.Lock()
    defer reqLimit.Lock.Unlock()
    if reqLimit.ReqCount < reqLimit.MaxCount {
        reqLimit.ReqCount += 1
        return true
    } else {
        return false
    }
}
```

#### 令牌桶限流  

这里涉及的API
主要是`channel` ，利用的就是channel的阻塞操作。\
我们把一个指定尺寸channel，相当于一个指定容量的令牌桶，每一个空闲位置就是一个令牌。由于channel满时就无法向其中加元素，所以我们就可以以固定的速率消费channel中的消息（释放空间相当于添加令牌），取令牌就是添加一条消息，当令牌桶满时就无法正常添加消息（取令牌）了，这样就利用channel来构造了一个限流器。\
下面来看一下代码：

```  
type NotAbandonReqLimitService struct {
    TokenPool chan bool // 令牌tong
}
func CreateNewRequestLimitService(interval time.Duration, maxCnt int) *NotAbandonReqLimitService {
    reqLimit := &NotAbandonReqLimitService{}
    reqLimit.TokenPool = make(chan bool, maxCnt) // 令牌桶最大容量
    go func() {
        tmpStr := strconv.Itoa(maxCnt)
        maxCntInt64,_ := strconv.ParseInt(tmpStr, 10, 64)
        ticker := time.NewTicker( time.Duration(interval.Nanoseconds()/(maxCntInt64*1000*1000))* time.Millisecond) // 匀速添加令牌，1s/最大qps 就是添加的速率
        for {
            <- ticker.C
            <- reqLimit.TokenPool
        }
    }()
    return reqLimit
}
func (reqLimit *NotAbandonReqLimitService) GetTokenNotAbandonRequest() {
    reqLimit.TokenPool <- true // 消费令牌
}
```

在常用限流算法go的实现中，关于sync、chan的使用大致就是以上，这两个demo挺简单的，可以由浅入深帮助大家理解一下go并发编程及Mutex、chan
的API使用。\
关于限流算法暂时就说这么多。
:::

 
