# 守护进程

# Daemons

A daemon is a process running in the background. In NuttX a daemon process is a task, in POSIX (Linux / Mac OS) a daemon is a thread.
守护进程“daemon”是运行在后台的进程。
New daemons are created through the `px4_task_spawn()` command.在NuttX中守护进程是一个任务，在POSIX(Linux/Mac OS)中是一个线程

```C++
daemon_task = px4_task_spawn_cmd("commander",
			     SCHED_DEFAULT,
			     SCHED_PRIORITY_DEFAULT + 40,
			     3600,
			     commander_thread_main,
			     (char * const *)&argv[0]);
```

The arguments here are:
以下是参数：
- arg0: the process name, `commander`
- arg1: the scheduling type (RR or FIFO)
  - arg2: the scheduling priority
  - arg3: the stack size of the new process or thread
  - arg4: the task / thread main function
  - arg5: a void pointer to pass to the new task, in this case holding the commandline arguments.
  - - arg0: the process name, `commander`
- arg0: 进程名 `commander`
- arg1: 调度类型（RR or FIFO）the scheduling type (RR or FIFO)
  - arg2: 调度优先级
  - arg3: 新进程或线程堆栈大小
  - arg4: 任务/线程主函数
  - arg5: 一个void指针传递给新任务,在这种情况下是命令行参数
