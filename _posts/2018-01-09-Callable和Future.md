---
layout: post
title: "Callable和Future"
date: 2018-01-09
description: "Callable和Future"
tag: Java并发
---

之前项目中用到了一些多线程的常用类。本次有空总结下。

Callable: 
类似于Runnable. 但是Runnable的run()没有返回值。并且无法抛出异常。
而Callable的功能更强大一些。它的call()可以返回范型。并且返回的值可以被Future拿到。
(FutureTask 实现了 RunnableFuture, RunnableFuture继承了Runnable和Future)。
也就是说Future可以拿到异步执行任务的返回值。

一.下面来看一个简单的例子:


public class CallableAndFuture {

    public static void main(String[] args) {
        Callable<Integer> callable = new Callable<Integer>() {
            public Integer call() throws Exception {
                return new Random().nextInt(100);
            }
        };
        FutureTask<Integer> future = new FutureTask<Integer>(callable);
        new Thread(future).start();
        try {
            Thread.sleep(5000);// 可能做一些事情
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

FutureTask实现了两个接口，Runnable和Future，所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值，那么这个组合的使用有什么好处呢？假设有一个很耗时的返回值需要计算，并且这个返回值不是立刻需要的话，那么就可以使用这个组合，用另一个线程去计算返回值，而当前线程在使用这个返回值之前可以做其它的操作，等到需要这个返回值时，再通过Future得到，岂不美哉！


二.下面来看另一种方式使用Callable和Future，通过ExecutorService的submit方法执行Callable，并返回Future.

public class CallableAndFuture {

    public static void main(String[] args) {
        ExecutorService threadPool = Executors.newSingleThreadExecutor();
        Future<Integer> future = threadPool.submit(new Callable<Integer>() {
            public Integer call() throws Exception {
                return new Random().nextInt(100);
            }
        });
        try {
            Thread.sleep(5000);// 可能做一些事情
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

代码是不是简化了很多，ExecutorService继承自Executor，它的目的是为我们管理Thread对象，从而简化并发编程，Executor使我们无需显示的去管理线程的生命周期，是JDK 5之后启动任务的首选方式。






