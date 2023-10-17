# 一、 实例
在写Separate downloaded files的时候，在recurringjob去下载那些状态为waitingfordownload的记录，（这些状态为waitingfordownload是记录文件为空的，或者总结失败的）

因为下载的时间长，数据多，因此考虑建一个新的队列来进行会议记录的下载（就是下方的：InternalHostingDownloadMeetingFilesQueue）

在写下载的方法时，学到了一个新的方式

```
  private void DownloadWorkWeChatMeetingRecords(List<TencentMeetingRecord> records)
    {
        if (records.Count <= 0) return;
        
        var parentJobId = _postBoyBackgroundJobClient.Enqueue(() => _weChatService.GetDownloadAddressAndDownloadFilesAsync(
            records.First(), CancellationToken.None), HangfireQueues.InternalHostingDownloadMeetingFilesQueue);
                    
        records.Skip(1).Aggregate(parentJobId, (current, record) =>
            _postBoyBackgroundJobClient.ContinueJobWith(current, () => _weChatService.GetDownloadAddressAndDownloadFilesAsync(
                record, CancellationToken.None), HangfireQueues.InternalHostingDownloadMeetingFilesQueue));
    }
```

在给定的代码中，第一条 job 完成后，它会被跳过（通过 .Skip(1)）

然后使用 Aggregate 方法将第一条和第二条 job 一起传递给 ContinueJobWith 方法，以确保它们按顺序连接在一起执行。

接着，第二条 job 完成后，它会被跳过，然后与第三条 job 一起传递给 ContinueJobWith 方法，再次确保它们按顺序连接在一起执行。这个过程会一直持续下去，直到所有的 job 都被处理完毕，而且它们都会按照特定的顺序一个接一个地执行，而不会并行执行。

* Aggregate 是一个聚合函数，它接受一个累加器和一个方法，用于将累加器与集合中的每个元素组合。在这里，累加器 current 是 parentJobId，并且每个 record 都会与它组合，通过 ContinueJobWith 方法启动一个后续的任务。

具体来说，每次 Aggregate 中的委托调用，都会将 current（初始值为 parentJobId）与当前的 record 一起传递给 ContinueJobWith 方法。ContinueJobWith 方法的作用是将两个后台作业连接在一起，以确保它们按顺序执行，而不是并行执行。这是一种将多个后台任务按顺序串联起来执行的方式。

# 二、举一个简单明了的例子：
假设x集合包含了多个 job，假设你有以下 5 个 job：
Job1 Job2 Job3 Job4 Job5

现在，x.Skip(1) 是为了跳过第一个 job（Job1）。因此，现在x 集合包含了以下 job：
Job2 Job3 Job4 Job5

然后，使用 Aggregate 方法，将这些 job 一个接一个地连接在一起。Aggregate 从第一个 job（Job2）开始，然后将它与下一个 job（Job3）连接在一起，以确保它们按照顺序执行。接着，第三个 job（Job4）与前面的连接在一起，以此类推。最终，整个 job 流程就会按照特定顺序一个接一个地执行。(代码中的 x.Skip(1) 是为了确保第一个 job 不会重复执行，而是在 Aggregate 中将其与下一个 job 连接在一起，以确保它们按顺序执行)
