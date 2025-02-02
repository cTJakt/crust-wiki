---
id: sWorkerOverview
title: 概览
sidebar_label: 概览
---

## 什么是sWorker

sWorker（Storage Worker）是对节点的存储工作量进行校验和量化，并将存储工作量上报到Crust区块链的组件。它实现了Crust网络的MPoW（Meaningful Proof of Work）机制，是构建Crust网络的核心组件之一。sWorker需要解决去中心化存储的一个核心问题：工作量的校验和量化。这个问题可以描述为，节点A声称自己有10TB的存储容量，那么如何去验证A确实拥有这10TB的容量呢？让一个节点自证工作量？这明显行不通，为了利益，节点必然会作弊。那么既然“自证”不靠谱，就只能用“他证”来解决，即从一个第三者的视角来证明。这个“他证”的他，既可以是另外的节点，也可以是一个可信任的程序，从这两个角度出发，产生了两种去中心化存储工作量证明的方法：零知识证明和TEE技术（Trusted Execution Environment，可信执行环境技术）。sWorker使用TEE技术进行研发，该技术具有成本低、网络负担小和校验性能高等优点。

> **零知识证明**：可以理解为不需要除了事物本身外的额外信息就可以证明，举个例子，节点A声称有文件X，文件X由1000个文件碎片组成，节点B也有文件X，它想证明A是否有文件X，那么节点B可以向A索要X的某个随机的文件碎片，比如第100片，B将索取到的X的第100片碎片和自己存储的X的第100片碎片进行比较，不相等，就说明A没有文件X。整个证明过程只需要X本身的信息（碎片），就可以证明A是否拥有X。要是B帮A作弊怎么办？没关系还有C也可以来校验A，C被买通了也还有D，如此循环，网络中的节点越多，A作弊的代价就越大。最终的结果，就是所有节点为了维护自身的利益，都会诚实地证明。可以说零知识证明是区块链思想的一种成功应用，这种方式更加的安全，缺点也是显而易见，为了证明A有文件X，可能全网的节点都要来向A索取文件X的碎片，这就造成了网络中存在大量的验证请求，随着文件数量增多，请求也会成倍增长，最后会导致网络拥堵。

> **TEE技术**：能够让节点运行一个“诚实”的程序，它不会说谎，节点A有多少工作量它就会量化多少。这个诚实的程序不受计算机上最高权限所有者的影响，即root权限也不能对它进行任何的修改和窥探。有了这个诚实的程序，节点A就可以在自己的机器上完成对存储工作量的证明，极大地减轻了网络的压力。缺点也是有的，首先程序的设计就会比较复杂，需要缜密地设计程序与外部的数据交互接口。其次，TEE技术存在被破解的风险，一旦被破解将对整个网络造成灾难性的后果。但就目前而言，这两个缺点都是可以克服的。


## sWorker是如何工作的

### 增加工作量
sWorker使用TEE技术进行研发，因为sWorker对在其外所生成的存储工作量都不信任，因而sWorker只能自己生成数据来增加工作量。sWorker生成工作量的方式有两种：SRD（Sealed Random Data）和seal file。

1. SRD通过随机生成的数据来增加工作量，随机数据会在sWorker中生成，然后存储到磁盘中，而随机数据的哈希值作为该数据的“凭证”会被保存在sWorker中，通过“凭证”，sWorker可以对随机数据进行验证。
1. seal file是通过对有意义文件的加密来增加工作量，具体过程是文件块被读入sWorker中，在sWorker中进行seal（加密）（注：值得注意的是，对相同的文件块进行无数次的seal，每次得到的加密文件块都不相同，这可以防止生成攻击和女巫攻击），加密后的文件块被存储到外部，加密块的“凭证”则被保存在sWorker中，用来对加密文件块进行校验。SRD和seal file的过程比较复杂，后续会有详细的解释说明，在此不进行赘述。

### 上报工作量
每隔一段时间，sWorker会将存储工作量上报到Crust区块链上。为了让Crust区块链能够识别sWorker的工作量，sWorker在启动之后的第一件事就是“入网”。“入网”是将sWorker的身份和Crust链上身份进行绑定的过程，每一个sWorker都有自己的身份，它上报的工作量属于某个Crust账户。工作量上报到Crust区块链后，需要进行签名校验和工作量校验，通过之后，才能将上报的工作量记入区块链中。


## 其他功能

### 元数据持久化
在sWorker中会存储一些不能被外部篡改和知晓的信息，比如用于工作量签名的私钥，SRD和seal file的存储凭证等等，这些数据会保存在sWorker运行时的加密空间中。为了防止这些数据在程序异常退出或者机器断电等一系列意外中丢失，sWorker会定期将这些数据进行加密，并存储到磁盘中。这里使用的加密方式是TEE技术提供的seal算法，seal是需要硬件支持才能够完成的加解密操作，并且被加密的数据只能被加密它的程序解密。所以sWorker能在意外中断之后重启，并且恢复之前的工作数据。

### 升级
sWorker的升级需要在Crust区块链的配合下才能够完成，升级完成之后，新的sWorker将会继承旧sWorker的工作量。
