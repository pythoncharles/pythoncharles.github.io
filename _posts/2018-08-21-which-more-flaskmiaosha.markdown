---
layout: post
category: web
title:  "Flask - 抢购、秒杀系统"
tags: [阅读,网络]
date: 2018-08-21 13:05:25
---

&emsp;&emsp;所谓“秒杀”，就是网络卖家发布一些超低价格的商品，所有买家在同一时间网上抢购的一种销售方式。通俗一点讲就是网络商家为促销等目的组织的网上限时抢购活动。
由于商品价格低廉，往往一上架就被抢购一空，有时只用一秒钟。2011年以来，在淘宝等大型购物网站中，“秒杀店”的发展可谓迅猛。
<!-- more -->

目录
一、秒杀系统需求分析    
1-1 超卖问题   
1-2 超抢问题   
二、基于 Redis 队列实现抢购   
2-0 思路总结    
2-1 初始版本    
2-2 添加抢购量需求    
三、Jmeter 并发测试工具    
3-1 Jmeter 汉化    
3-2 随机参数（函数）    
3-3 响应编码问题（Unicode 转换）    
四、基于 Redis 的计数器实现秒杀    
4-0 Redis 的线程安全    
4-1思路总结    
--------------------------------------------------------------------------------
 
##### 一、秒杀系统需求分析    
1-1 超卖问题    
10个物品被20个用户抢到。    
查询出对应商品的库存，看是否大于0，然后执行生成订单等操作，但是在判断库存是否大于0处，如果在高并发下就会有问题，导致库存量出现负数（线程安全）
解决思路：解决线程安全。
思路一、由于Redis 是单进程单线程，Redis 的所有基础命令都是原子性的，所以在单命令下线程安全。
php 实现参考    
1-2 超抢问题    
对于同一个物品，单用户能抢到多个。    
解决思路：判断用户是否抢到，需要记录抢购用户    
##### 二、基于 Redis 队列实现抢购    
使用redis队列，因为pop操作是原子的，即使有很多用户同时到达，也是依次执行。    
mysql事务在高并发下性能下降很厉害，文件锁的方式也是    
并发思路：celery 异步任务（优化高并发场景） + redis 队列    
2-0 思路总结    
key - value []    
假设：商品A限购5个，初始化list在redis内    
抢购商品A - [1,1,1,1,1]    
开始抢购则进行pop操作，每来一个用户先判断list的长度是否非0,若非0，pop一个，并将用户名写入另一个list    
抢购商品A - [1,1,1,1]    
商品A已抢用户 - [用户1，]    
若list（抢购商品A ）已经pop空，则将返回用户失败信息。        
2-1 初始版本   
``` 
# ---------用于生成抢购库存列表-----------------
li = [1 for i in range(50)]
print(li)
re = conn.lpush('goods_a', *li)
print(re)

 

# API 接口，CBV
class Seckill(Resource):
    def post(self):
        # 获取form表单内的username,唯一参数
        username = request.form['username']
        # redis 内list存储数据为byte类型
        username = bytes(username, encoding='utf-8')
 
        # 获得当前redis内已购用户信息
        users_list = redis_store0.lrange('a_users', 0, -1)
        if username in users_list:
            return '您已经抢到了，请勿重复请求。'
 
        # 判断库存
        goods_count = redis_store0.llen('goods_a')
        if goods_count:
            res = redis_store0.lpop('goods_a')
            if res:
                redis_store0.lpush('a_users', username)
                return '恭喜您，您已抢到。'
        return '很遗憾，抢购已经结束。'
```

2-2 添加抢购量需求    
用户可自主选择抢购数量，限制为1个或2个。    
改动    
使用hash为用户存储。    
用户判断，为是否查询得到对应的key为依据。    
```
from flask import Blueprint, request
from flask_restful import Resource
from extensions import redis_store0
 
from views import Api
 
seckill_blueprint = Blueprint('seckill', __name__)
seckill_api = Api(seckill_blueprint)
 
 
class Seckill(Resource):
    # 通过队列实现秒杀
    def post(self):
        # 获取form表单内的username,唯一参数
        username = request.form['username']
        # redis 内list存储数据为byte类型
        username = bytes(username, encoding='utf-8')
 
        # 用户选择购买的数量，1 or 2
        purchase = request.form['purchase']
        if purchase not in ['1', '2']:
            return '对不起，请选择正确的抢购数量！'
 
        # 获得当前redis内已购用户信息
        users_dic = redis_store0.hget('a_users',username)
        # 若不存在返回 None
        if users_dic :
            return '您已经抢到了，请勿重复请求。'
 
        # 判断库存
        goods_count = redis_store0.llen('goods_a')
        if goods_count:
            if goods_count < int(purchase):
                return '不好意思，库存不足了，请减少数量再次请求。'
            res = redis_store0.lrem('goods_a', purchase, 1)
            if res:
                redis_store0.hset('a_users', username, purchase)
                return '恭喜您，您已抢到。'
        return '很遗憾，抢购已经结束。'
seckill_api.add_resource(Seckill, '/seckill')
```
##### 三、Jmeter 并发测试工具
jmeter 官方下载
使用参考博客
3-1 Jmeter 汉化

 


3-2 随机参数（函数）

3-3 响应编码问题（Unicode 转换）
解决参考
```
private static String ascii2native ( String asciicode )
{
    String[] asciis = asciicode.split ("\\\\u");
    String nativeValue = asciis[0];
    try
    {
        for ( int i = 1; i < asciis.length; i++ )
        {
            String code = asciis[i];
            nativeValue += (char) Integer.parseInt (code.substring (0, 4), 16);
            if (code.length () > 4)
            {
                nativeValue += code.substring (4, code.length ());
            }
        }
    }
    catch (NumberFormatException e)
    {
        return asciicode;
    }
    return nativeValue;
}
String asciicode =new String(prev.getResponseData(),"UTF-8");
prev.setResponseData(ascii2native(asciicode));
```
重启生效：sampleresult.default.encoding=UTF-8

##### 四、基于 Redis 的计数器实现秒杀
4-0 Redis 的线程安全    
参考学习    
Redis的操作之所以是原子性的，是因为Redis是 单线程的。    
但是 在多命令下的Redis 是非原子性的。    
4-1思路总结    
incr(self, name, amount=1) - 整数自增    
decr(self, name, amount=1) - 自减    
set(self, name, value, ex=None, px=None, nx=False, xx=False) - 设置库存，由于自减到0字段无法消失，需要手动设置过期时间。    
实现了用户选择购买个数，1 or 2 的个数选择    
注意！！！可能存在多进程的数据非一致性，导致非幂等（未验证）    
分布式系统下，用户a同时发送多次请求，服务器a、b获取请求，并获取到的redis数据字符相同，都会进入判断执行 decr 操作进行货品自减，
而实际上，b服务器已经执行了自减，数据字符已为0，但a仍能执行decr操作，导致数据变为负数。    
错误原因：decr自减存在负数，并返回永远为true。    
```
from flask import Blueprint, request
from flask_restful import Resource
from extensions import redis_store0
 
from views import Api
 
seckill_blueprint = Blueprint('seckill', __name__)
seckill_api = Api(seckill_blueprint)
 
 
class SeckillIncr(Resource):
    # 通过计数器实现秒杀
    def post(self):
        username = request.form['username']
        # redis 内list存储数据为byte类型
        username = bytes(username, encoding='utf-8')
 
        # 用户选择购买的数量，1 or 2
        purchase = request.form['purchase']
        if purchase not in ['1', '2']:
            return '对不起，请选择正确的抢购数量！'
 
        # 获得当前redis内已购用户信息
        users_dic = redis_store0.hget('a_users', username)
        if users_dic:
            return '您已经抢到了，请勿重复请求。'
 
        # 判断库存
        goods_count = redis_store0.get('goods_a').decode('utf-8')
        if int(goods_count) > 0:
            if int(goods_count) < int(purchase):
                return '不好意思，库存不足了，请减少数量再次请求。'
            res = redis_store0.decr('goods_a', purchase)
            if res:
                redis_store0.hset('a_users', username, purchase)
                return '恭喜您，您已抢到。'
        return '很遗憾，抢购已经结束。'
 
 
seckill_api.add_resource(SeckillIncr, '/incr')
```
