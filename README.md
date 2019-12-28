# homework_final
---

## <center> C++ 面向对象程序设计期末报告 </center>

### 班级：18063111 &nbsp; &nbsp;学号：18062005 &nbsp;&nbsp; 姓名：林紫颖

---

* 1 [项目简介](https://github.com/UpCris/homework_final/blob/master/README.md#%E9%A1%B9%E7%9B%AE%E7%AE%80%E4%BB%8B)
  * 1.1 [项目背景](https://github.com/UpCris/homework_final/blob/master/README.md#%E9%A1%B9%E7%9B%AE%E7%AE%80%E4%BB%8B)
  * 1.2 [项目需求](https://github.com/UpCris/homework_final/blob/master/README.md#%E9%A1%B9%E7%9B%AE%E9%9C%80%E6%B1%82)
  * 1.3[环境依赖](https://github.com/UpCris/homework_final/blob/master/README.md#%E7%8E%AF%E5%A2%83%E4%BE%9D%E8%B5%96)
* 2 [项目内容 ](https://github.com/UpCris/homework_final/blob/master/README.md#%E7%8E%AF%E5%A2%83%E4%BE%9D%E8%B5%96)
  * 2.1[测试案例](https://github.com/UpCris/homework_final/blob/master/README.md#%E6%B5%8B%E8%AF%95%E6%A1%88%E4%BE%8B)
  * 2.2[功能添加](https://github.com/UpCris/homework_final/blob/master/README.md#%E5%8A%9F%E8%83%BD%E6%B7%BB%E5%8A%A0)
  * 2.3[测试效果](https://github.com/UpCris/homework_final/blob/master/README.md#%E6%B5%8B%E8%AF%95%E6%95%88%E6%9E%9C)
* 3 [遇到的问题及建议](https://github.com/UpCris/homework_final/blob/master/README.md#%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98)
* 4 [心得体会](https://github.com/UpCris/homework_final/blob/master/README.md#%E5%BF%83%E5%BE%97%E4%BD%93%E4%BC%9A)
  * 4.1 [收获](https://github.com/UpCris/homework_final/blob/master/README.md#%E6%94%B6%E8%8E%B7)
  * 4.2 [备注](https://github.com/UpCris/homework_final/blob/master/README.md#%E5%A4%87%E6%B3%A8)
* 5 [其他](https://github.com/UpCris/homework_final/blob/master/README.md#%E5%85%B6%E4%BB%96)
---

---
## 项目简介


### 项目背景：

**Nebula Graph** 是一款开源的图数据库，擅长处理千亿个顶点和万亿条边的超大规模数据集。

与其他图数据库产品相比，**Nebula Graph** 具有如下优势：

   - 全对称分布式架构
   - 存储与计算分离
   - 水平可扩展性
   - RAFT 协议下的数据强一致
   - 类 SQL 查询语言
   - 用户鉴权


更多关于[Nebula Graph](https://github.com/vesoft-inc/nebula)的内容

### 项目需求：

1. 熟悉linux环境，灵活运用linux命令。
1. 熟悉github开源及相关基本操作。
1. 运用C++面向对象编程设计知识完善、改进nebula项目。

### 环境依赖：

1. 操作系统： Ubuntu 18.04

1. 相关linux、git命令:

```bash
bash> cmake ..
bash> make
bash> sudo make install
```

```bash
bash> git clone https://github.com/vesoft-inc/nebula.git
bash> git init
bash> git clone
bash> git config
bash> git add
bash> git rm 
bash> git mv 
bash> git commit
bash> git branch
bash> git checkout
bash> git log
bash> git status
bash> git diff
bash> git pull
bash> git push
```

[相关github学习](https://github.com/silverdays/github-lecture)的内容

---

## 项目内容：

- [分支myfeature](https://github.com/UpCris/nebula/tree/myfeature)地址

### 测试案例：

> ~/nebula-master/src/common/network/test/NetworkUtilsTest.cpp

```C++
/* Copyright (c) 2018 vesoft inc. All rights reserved.
 *
 * This source code is licensed under Apache 2.0 License,
 * attached with Common Clause Condition 1.0, found in the LICENSES directory.
 */

#include "base/Base.h"
#include <gtest/gtest.h>
#include "network/NetworkUtils.h"
#include <iostream>
#include <ctime>

namespace nebula {
namespace network {

TEST(NetworkUtils, getHostname) {
    std::string hostname = NetworkUtils::getHostname();

    time_t begin, end;
    double ret;
    begin = clock();
    FILE* fp = popen("LD_PRELOAD= hostname | tr -d ['\n']", "r");
    char buffer[256];
    auto numChars = fgets(buffer, sizeof(buffer), fp);
    UNUSED(numChars);
    pclose(fp);
    EXPECT_EQ(std::string(buffer), hostname);
    end = clock();
    ret = double (end-begin)/CLOCKS_PER_SEC;
    std::cout << "runtime: " << ret << std::endl;

}


TEST(NetworkUtils, getIPv4FromDevice) {
    {
        auto result = NetworkUtils::getIPv4FromDevice("lo");
        ASSERT_TRUE(result.ok()) << result.status();
        ASSERT_EQ("127.0.0.1", result.value());
    }
    {
        auto result = NetworkUtils::getIPv4FromDevice("any");
        ASSERT_TRUE(result.ok()) << result.status();
        ASSERT_EQ("0.0.0.0", result.value());
    }
    {
        auto result = NetworkUtils::getIPv4FromDevice("non-existence");
        ASSERT_FALSE(result.ok()) << result.status();
    }
}


TEST(NetworkUtils, listIPv4s) {
    auto result = NetworkUtils::listIPv4s();
    ASSERT_TRUE(result.ok()) << result.status();
    ASSERT_FALSE(result.value().empty());
    auto found = false;
    for (auto &ip : result.value()) {
        if (ip == "127.0.0.1") {
            found = true;
        }
    }
    ASSERT_TRUE(found);
}


TEST(NetworkUtils, listDeviceAndIPv4s) {
    auto result = NetworkUtils::listDeviceAndIPv4s();
    ASSERT_TRUE(result.ok()) << result.status();
    ASSERT_FALSE(result.value().empty());
    ASSERT_NE(result.value().end(), result.value().find("lo"));
}


TEST(NetworkUtils, intIPv4Conversion) {
    IPv4 ip;
    ASSERT_TRUE(NetworkUtils::ipv4ToInt("127.0.0.1", ip));
    EXPECT_EQ(NetworkUtils::intToIPv4(ip), "127.0.0.1");

    ip = 0x11223344;
    IPv4 converted;
    ASSERT_TRUE(NetworkUtils::ipv4ToInt(NetworkUtils::intToIPv4(ip), converted));
    EXPECT_EQ(converted, ip);
}


TEST(NetworkUtils, getDynamicPortRange) {
    uint16_t low, high;
    ASSERT_TRUE(NetworkUtils::getDynamicPortRange(low, high));
    ASSERT_NE(low, high);
}


TEST(NetworkUtils, getAvailablePort) {
    time_t begin, end;
    double ret;
    begin = clock();
    auto port = NetworkUtils::getAvailablePort();
    ASSERT_GT(port, 0);
    end = clock();
    ret = double (end-begin)/CLOCKS_PER_SEC;
    std::cout << "runtime: " << ret << std::endl;
}


TEST(NetworkUtils, toHosts) {
    std::string hostsString = "192.168.1.1:10001, 192.168.1.2:10002, 192.168.1.3:10003";
    auto addresRet = NetworkUtils::toHosts(hostsString);
    ASSERT_TRUE(addresRet.ok());
    std::vector<HostAddr> hosts = std::move(addresRet.value());
    EXPECT_EQ(3, hosts.size());
    IPv4 ip;
    NetworkUtils::ipv4ToInt("192.168.1.1", ip);
    int32_t count = 0;
    for (auto& host : hosts) {
        EXPECT_EQ(ip + count, host.first);
        EXPECT_EQ(10001 + count, host.second);
        count++;
    }
    EXPECT_STREQ(hostsString.c_str(), NetworkUtils::toHosts(hosts).c_str());
}

}   // namespace network
}   // namespace nebula


int main(int argc, char** argv) {
    testing::InitGoogleTest(&argc, argv);
    folly::init(&argc, &argv, true);
    google::SetStderrLogging(google::INFO);
    time_t tt;
    time( &tt );
    tt = tt + 8*3600;  // transform the time zone
    tm* t= gmtime( &tt );
    std::cout << tt << std::endl;

    printf("%d-%02d-%02d %02d:%02d:%02d\n",
           t->tm_year + 1900,
           t->tm_mon + 1,
           t->tm_mday,
           t->tm_hour,
           t->tm_min,
           t->tm_sec);

    return RUN_ALL_TESTS();
}

```

### 功能添加

1. 显示本地时间

1. 计算各测试所需时间

### 测试效果

- 源码修改前编译结果：

![](https://github.com/UpCris/homework_final/blob/master/original_test.png)

- 源码修改后编译结果：

![](https://github.com/UpCris/homework_final/blob/master/result_3.png)

- ![git commit](https://github.com/UpCris/homework_final/blob/master/commit.png)

- ![git push](https://github.com/UpCris/homework_final/blob/master/push.png)

---

## 遇到的问题及建议

1. 不理解**Nebula Graph**，开发缺少方向感。

1. 缺乏C++面向对象编程的思维。

1. 精通C++开发需要大量时间，建议不要在大二开课。

---

## 心得体会 

### 收获

1. 熟悉markdown语言及在github上交作业。

- （学会运用和理解各类命令，如上所述git命令及linux命令）

1. 作为一门大学本科课程极大地拓展了我的视野。

1. 了解了开发者必备的基本操作及开源的含义。

1. 学会了用`cpplint`检查代码规范性，长此以往对C++编程肯定有很大提升。

1. github上各类资源都很多，给了我一个很好的学习交流平台。

### 备注

- 鉴于这类的request应该有很多了，我就不pull上去打扰大家了。

- 总而言之真心感谢老师一学期以来的指导,我还是很开心的，毕竟能在很多同学那边吹一吹了♪(^∇^*)。

## 其他

> 仅供参考(一学期以来搜集的学习资料)

[C++面向对象编程讲义](https://github.com/UpCris/Cpp-0-1-Resource)

[github入门](https://github.com/UpCris/github-lecture)

[markdown进阶](https://github.com/UpCris/markdown-toc)

[Git的分支工作流与Pull Request](https://www.cnblogs.com/selimsong/p/9059964.html)


