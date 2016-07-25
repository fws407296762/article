1. `schedule.scheduleJob` 创建一个定时任务，第一个参数是定时的时间，第二个参数是一个 `function` ，就是执行的任务。
2. 定时的时间有几种形式：
    * `* * * * * *`，六个值，分别表示的是 `秒（0 - 59）、分（0 - 59）、时（0 - 23）、日期（1 - 31）、月份（1 - 12）、星期几（0 - 7）`
    * 日期，`new Date(2016,6,4,21,55,30)` 这个就表示在制定的日期去执行一个任务
    * 对象，可以指定某一个属性来循环执行任务 首先需要定义一个规则对象 `var rule = new schedule.RecurrenceRule()` 。然后指定规则属性： `rule.second = 0`，这个的意思是每次遇到秒为 **00** 的时候则执行任务。可以指定的属性有 `second`、`minute`、`hour`、`dayOfWeek`、`month`、`dayofMonth`。每一个属性都可以是 `数字`、`数组`。如果是数组的话，则表示每次到了数组的某一项则执行，例如 `[0,4,6,8,10]`，每次遇到 00 秒，04秒，08秒，10秒则执行任务
3. 有很多时候，我们希望先取消任务，然后执行完了某个事件之后再去把任务重新开启。例如我做个的一个功能遇到了一个问题：定时批量下载图片，当一个任务开启后，开始批量下载图片，但是下载图片的时间无法控制，因为你根本就不知道这个图片什么时候可以下载完，所以就会出现下载任务没有完成，但是又有一个任务开始了，这个时候就出现了混乱，所以我们希望下载任务开始的时候就将定时任务取消，然后在下载任务完成后定时任务开启。在 `node-schedule` 的文档里面只有 `cancle` 这个函数，但是没有重启的函数，所以就去看文档了，发现默认导出了几个公共的函数：`Job`、`Range`、`RecurrenceRule`、`Invocation`、`scheduleJob`、`rescheduleJob`、`scheduledJobs`、`cancelJob`。这几个函数都是 `node-schedule` 导出的对象可调用的函数，不过我们可以输出我们创建的任务对象看看有什么 `console.log(schedule.scheduleJob())`,发现有几个差不多的函数可以调用，`job`、`trackInvocation`、`trackInvocation`、`stopTrackingInvocation`、`triggeredJobs`、`setTriggeredJobs`、`cancel`、`cancelNext`、`reschedule`、`nextInvocation`、`pendingInvocations`，不过这里面我目前就知道几个函数的作用，`job` 是执行一下定时任务；`cancel` 取消定时任务，这个函数有一个参数，这个参数是一个 `Boolean` 值，如果是 `true` 则在取消后马上重启；`reschedule` 这个就是我们希望用到的函数了，这个函数必须有一个参数 `spec`，刚开始看源码的时候，根本就不知道是什么意思，后来才发现是要我输入定时的规则，就是第二条的定时时间。