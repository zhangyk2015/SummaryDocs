# ThreadPoolTaskScheduler

## 介绍

根据名字可以看出，这是一种将task委托到ThreadPool的scheduler机制。

## 核心代码

``` java
ScheduledFuture<?> schedule(Runnable task, Trigger trigger)
```

将任务写成Runnable形式，在使用cron表达式生成Trigger。如`new CronTrigger("30 30 8 ? * *) ` 也可以 `new CronTask(Runnable task, String CronExpression).getTrigger()`

## 用法

``` java
ThreadPoolTaskScheduler taskScheduler = new ThreadPoolTaskScheduler();
taskScheduler.setPoolSize(4);  // 最大线程数
taskScheduler.setRemoveOnCancelPolicy(true); 
taskScheduler.setThreadNamePrefix("TaskSchedulerThreadPool-"); // 线程池前缀名

CronTask cronTask = new CronTask(Runnable task, String CronExpression);

ScheduledFuture<?> scheduledFuture = taskScheduler.schedule(cronTask.getRunnable(), cronTask.getTrigger());

if (scheduledFuture != null) {
	scheduledFuture.cancel(true);  // 取消定时任务
}
```
