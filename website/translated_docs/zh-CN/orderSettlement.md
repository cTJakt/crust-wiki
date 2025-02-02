---
id: orderSettlement
title: Order Settlement Guidance
sidebar_label: Order Settlement Guidance
---

任意用户都可以对存储订单发起清算。存储订单清算除了作为[存储商户](storage-merchant.md)领取订单奖励的前置步骤以外，还可以**延续**有持续存储意愿的存储订单，**关闭**已到期且无续费意愿的存储订单，

## 订单清算条件和效果

订单清算机制在不同的条件下会产生不同的效果：

|    |续费池余额充足|续费池余额不足|
|:----|:----|:----|
|订单在有效期内|<ul><li>当前累计订单奖励分配给商户</li>|<ul><li>当前累计订单奖励分配给商户</li>|
|订单过期15天内|<ul><li>触发订单续费</li><li>清算人获得订单续费奖励</li><li>当前累计订单奖励分配给商</li>|<ul><li>订单关闭</li></ul><ul><li>当前累计订单奖励分配给商户</li></ul>|
|订单过期超过15天|<ul><li>触发订单续费</li></ul><ul><li>清算人获得订单续费奖励</li><li>清算人获得订单清算奖励</li><li>当前累计订单奖励扣除清算奖励后分配给商户</li></ul>|<ul><li>订单关闭</li></ul><ul><li>清算人获得订单清算奖励</li><li>当前累计订单奖励扣除清算奖励后分配给商户</li></ul>|

### 订单续费奖励

存储订单到期（目前每个文件订单到期时间为180天）且续费池余额充足时，第一个对该订单发起清算的用户，将会触发订单续费操作（效果等同于重新发起一笔存储订单）。同时，清算人将会获得一笔续费奖励。续费订单和给清算人的续费奖励均由订单的续费池支付。查看[存储用户指南](storageUserGuide.md)，了解关于“续费池”的详细说明。

### 订单清算奖励

当存储订单过期时间超过15天后，任意用户对文件发起清算将可以获得一定比例的清算奖励。清算奖励计算满足如下规则：

1. 订单清算奖励在订单过期时间15天时初始为0；
2. 订单清算奖励随着时间线性增加；
3. 订单清算奖励在文件订单过期30天时达到最大，最大值为当前累计订单奖励；
4. 当前累计订单奖励扣除订单清算奖励后，分配给存储商户。

查看[存储商户指南](merchantGuidance.md)，了解关于“当前累计订单奖励”的详细说明。

## 订单清算操作说明

用户可以进入[Crust Apps](https://apps.crust.network/#/market/settlements) -> 存储市场 -> 存储商户 -> 订单清算，通过“拉取”功能获取希望查看的订单列表并进行清算。如下图：

![dsm](assets/merchant/settlement.png)

“总收益数” 一栏显示了这笔清算操作可以获得的最终收益，也就是**订单续费奖励+订单清算奖励。**用户只需点击右边的“清算”按钮来进行清算，并获得收益。**清算需要发起交易，并支付交易手续费，有些订单的总清算奖励可能低于手续费，这些订单的清算可能导致清算人负收益，用户可以通过[存储市场权益](dsm-benefits.md)功能，获得清算手续费的减免。**
