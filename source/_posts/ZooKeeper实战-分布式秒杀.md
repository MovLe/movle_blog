---
title: 'ZooKeeper实战-分布式秒杀'
date: 2019-01-03 15:50:00
tags: 
- ZooKeeper
- ZooKeeper实战
categories: ZooKeeper
---

#### 1.添加所需依赖：

pom.xml
```xml
         <!-- zookeeper所需依赖 -->
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-client</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>16.0.1</version>
        </dependency>

        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.10</version>
        </dependency>
```


#### 2.编写代码：
##### (1)不启动锁：
```java
package SimpleZKlock;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.recipes.locks.InterProcessMutex;
import org.apache.curator.retry.ExponentialBackoffRetry;

public class TestDistributedLock {

    //定义共享资源
    private static int count = 10;

    private static void printCountNumber() {
        System.out.println("***********" + Thread.currentThread().getName() + "**********");
        System.out.println("当前值：" + count);
        count--;

        //睡2秒
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println("***********" + Thread.currentThread().getName() + "**********");
    }


    public static void main(String[] args) {
        //定义客户端重试的策略
        RetryPolicy policy = new ExponentialBackoffRetry(1000,  //每次等待的时间
                10); //最大重试的次数

        //定义ZK的一个客户端
        CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString("192.168.1.121:2181")
                .retryPolicy(policy)
                .build();

        //在ZK生成锁  ---> 就是ZK的目录
        client.start();
        final InterProcessMutex lock = new InterProcessMutex(client, "/mylock");

        // 启动10个线程去访问共享资源
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {

                public void run() {
                    try {
                        //请求得到锁
                        //lock.acquire();
                        //访问共享资源
                        printCountNumber();
                    } catch (Exception ex) {
                        ex.printStackTrace();
                    } finally {
                        //释放锁
                        try {
                            // lock.release();
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    }
                }
            }).start();
        }
    }
}
```
![不启用锁](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBkNmY0MGY4OTMzMjc0OTIucG5n?x-oss-process=image/format,png)


##### (2)启用锁：

```java
package SimpleZKlock;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.recipes.locks.InterProcessMutex;
import org.apache.curator.retry.ExponentialBackoffRetry;

public class TestDistributedLock {

    //定义共享资源
    private static int count = 10;

    private static void printCountNumber() {
        System.out.println("***********" + Thread.currentThread().getName() + "**********");
        System.out.println("当前值：" + count);
        count--;

        //睡2秒
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println("***********" + Thread.currentThread().getName() + "**********");
    }


    public static void main(String[] args) {
        //定义客户端重试的策略
        RetryPolicy policy = new ExponentialBackoffRetry(1000,  //每次等待的时间
                10); //最大重试的次数

        //定义ZK的一个客户端
        CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString("192.168.1.121:2181")
                .retryPolicy(policy)
                .build();

        //在ZK生成锁  ---> 就是ZK的目录
        client.start();
        final InterProcessMutex lock = new InterProcessMutex(client, "/mylock");

        // 启动10个线程去访问共享资源
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {

                public void run() {
                    try {
                        //请求得到锁
                        lock.acquire();
                        //访问共享资源
                        printCountNumber();
                    } catch (Exception ex) {
                        ex.printStackTrace();
                    } finally {
                        //释放锁
                        try {
                            lock.release();
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    }
                }
            }).start();
        }
    }
}
```
![启用锁](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg5MmEwZDNjOGYyNzBkODMucG5n?x-oss-process=image/format,png)