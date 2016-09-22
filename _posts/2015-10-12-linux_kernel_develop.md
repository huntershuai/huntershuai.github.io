---
title: Linux Kernel Development
layout: posts
author: huntershuai
---

Linux 内核设计与实现
===
Linux Kernel Development
---
Tag：linux

Author: Robert Love

command:

- ps-el ：查看系统中的进程列表
- ps -eo state,uid, pid, ppid, rtprio, time, comm

## kernel introduction
- processor state:
     1. user mode, process state
     2. kernel mode, process state
     3. kernel mode, interrupt state

- kernel type:
    1. single kernel: share common address space
    2. micro kernel: separate address space, IPC
- linux & Unix difference
     1. load kernel module dynamic
     2. symmetry multiple process(SMP)
     3. preemptive
     4. common treat with all kernel thread
     5. object oriented device module and etc
     6. drop out-date Unix design
     7. freedom

- where is the source code can be located???/usr/src/linux

- kernel configuration
- kernel installation

### kernel development
1. no C lib or standard header file
2. GNU C
3. no float compute
4. short and  fixed-length stack
5. synchronization and co-working

#### GNU C
- inline function
inline is preferred in kernel development than Macro
inline usually defined in header file in the form of static function,
?using "static" before inline, the compiler will not create an function entity for inline function -->page 17

- likely( ) & unlikely( )
optimize the condition branch

- No memory protection
without memory page mechanism

## process management
In linux, thread is just the special process

### 进程描述符
- 双向队列存储进程描述符
- 进程描述符存放至内核栈的尾端（x86系统），power pc下有专门寄存器保存进程描述符

- process state:
     - TASK_RUNNING
     - TASK_INTERRUPTIBLE
     - TASK_UNINTERRUPTIBLE
     - _TASK_TRACED
     - _TASK_STOPPED

- 进程家族树
    - init 进程：内核启动最后阶段启动init进程

    - 访问父进程：
          struct task_struct *myparent=current->parent
    - 遍历访问子进程：
          struct task_struct *task;
          struct list_head *list;
          list_for_each(list,&current->childred)
          {
               task=list_entry(list, struct task_struct, sibling)  //??what meaning
          }

     - 写时拷贝：
          进程创建被拆解为fork过程和exec过程
          fork调用时推迟甚至免除拷贝数据的技术。实际开销为负值父进程的页表以及为子进程创建唯一进程描述符
     - fork, vfork
          根据各自参数标志调用clone()，由clone调用do_fork()，在其函数体内调用copy_process()，成功返回后调用子进程执行。
          >内核让子进程优先执行，一般子进程都会马上调用exec() function，可以避免写时拷贝的额外开销，如果父进程先执行，可能会开始向地址空间写入
          vfork 与fork的区别：
               不拷贝父进程页表项

          clone(SIGCHLD,0)//fork
          clone(CLONE_VFORK|CLONE_VM|SIGCHLD,0)//vfork
  - 线程实现
     在linux中，线程被视为特殊的进程，不做单独的结构区分，创建一个线程时，视为与父进程共享特定空间的进程。
     创建线程：
     clone(CLONE_VM|CLONE_FS|CLONE_FILES| CLONE_SIGHAND, 0)
     clone 标志表：page 27

- 内核线程
     - 内核线程只能由现有内核线程进行创建
     - 创建方法：
       struct task_struct   *kthread_create(int(*threadfn)(void *data), void *data, const char namefmt[],... ) //call clone create new process, and still wait to run
     - 运行：
          wake_up_process()

- 进程终结：
     do_exit()
     - ...
     - call exit_notify() 向父进程发送信号，为子进程寻找“养父”，养父为进程组中的其他进程或init进程。
     - call schedule, 置当前进程为EXIT_ZOMBIE退出状态，此时系统还保留有进程的内存（内核栈，thread_info， task_struct），
        最终有父进程检索并向内核上报此无用信息后，此部分内存才被释放。

- 孤儿进程:
     在进程终结前，必须为其子进程找好养父，否则其子进程在退出时处于ZOMBIE状态，但不能释放内存，成为孤儿进程。

## 进程调度



## System Call
	#define SYSCALL_DEFINE1(name, ...) SYSCALL_DEFINEx(1, _##name, __VA_ARGS__)
	#define SYSCALL_DEFINE2(name, ...) SYSCALL_DEFINEx(2, _##name, __VA_ARGS__)
	#define SYSCALL_DEFINE3(name, ...) SYSCALL_DEFINEx(3, _##name, __VA_ARGS__)
	#define SYSCALL_DEFINE4(name, ...) SYSCALL_DEFINEx(4, _##name, __VA_ARGS__)
	#define SYSCALL_DEFINE5(name, ...) SYSCALL_DEFINEx(5, _##name, __VA_ARGS__)
	#define SYSCALL_DEFINE6(name, ...) SYSCALL_DEFINEx(6, _##name, __VA_ARGS__)

Append:
- Task struct :
struct task_struct {
     volatile long state;     /* -1 unrunnable, 0 runnable, >0 stopped */
     void *stack;
     atomic_t usage;
     unsigned int flags;     /* per process flags, defined below */
     unsigned int ptrace;

	#ifdef CONFIG_SMP
     struct llist_node wake_entry;
     int on_cpu;
     unsigned int wakee_flips;
     unsigned long wakee_flip_decay_ts;
     struct task_struct *last_wakee;

     int wake_cpu;
	#endif
     int on_rq;

     int prio, static_prio, normal_prio;
     unsigned int rt_priority;
     const struct sched_class *sched_class;
     struct sched_entity se;
     struct sched_rt_entity rt;
	#ifdef CONFIG_CGROUP_SCHED
     struct task_group *sched_task_group;
	#endif
     struct sched_dl_entity dl;

	#ifdef CONFIG_PREEMPT_NOTIFIERS
     /* list of struct preempt_notifier: */
     struct hlist_head preempt_notifiers;
	#endif

	#ifdef CONFIG_BLK_DEV_IO_TRACE
     unsigned int btrace_seq;
	#endif

     unsigned int policy;
     int nr_cpus_allowed;
     cpumask_t cpus_allowed;

	#ifdef CONFIG_PREEMPT_RCU
     int rcu_read_lock_nesting;
     union rcu_special rcu_read_unlock_special;
     struct list_head rcu_node_entry;
     struct rcu_node *rcu_blocked_node;
	#endif /* #ifdef CONFIG_PREEMPT_RCU */
	#ifdef CONFIG_TASKS_RCU
     unsigned long rcu_tasks_nvcsw;
     bool rcu_tasks_holdout;
     struct list_head rcu_tasks_holdout_list;
     int rcu_tasks_idle_cpu;
	#endif /* #ifdef CONFIG_TASKS_RCU */

	#ifdef CONFIG_SCHED_INFO
     struct sched_info sched_info;
	#endif

     struct list_head tasks;
	#ifdef CONFIG_SMP
     struct plist_node pushable_tasks;
     struct rb_node pushable_dl_tasks;
	#endif

     struct mm_struct *mm, *active_mm;
     /* per-thread vma caching */
     u32 vmacache_seqnum;
     struct vm_area_struct *vmacache[VMACACHE_SIZE];
	#if defined(SPLIT_RSS_COUNTING)
     struct task_rss_stat     rss_stat;
	#endif
/* task state */
     int exit_state;
     int exit_code, exit_signal;
     int pdeath_signal;  /*  The signal sent when the parent dies  */
     unsigned long jobctl;     /* JOBCTL_*, siglock protected */

     /* Used for emulating ABI behavior of previous Linux versions */
     unsigned int personality;

     unsigned in_execve:1;     /* Tell the LSMs that the process is doing an
                    * execve */
     unsigned in_iowait:1;

     /* Revert to default priority/policy when forking */
     unsigned sched_reset_on_fork:1;
     unsigned sched_contributes_to_load:1;
     unsigned sched_migrated:1;

	#ifdef CONFIG_MEMCG_KMEM
     unsigned memcg_kmem_skip_account:1;
	#endif
	#ifdef CONFIG_COMPAT_BRK
     unsigned brk_randomized:1;
	#endif

     unsigned long atomic_flags; /* Flags needing atomic access. */

     struct restart_block restart_block;

     pid_t pid;
     pid_t tgid;

	#ifdef CONFIG_CC_STACKPROTECTOR
     /* Canary value for the -fstack-protector gcc feature */
     unsigned long stack_canary;
	#endif
     /*
     * pointers to (original) parent process, youngest child, younger sibling,
     * older sibling, respectively.  (p->father can be replaced with
     * p->real_parent->pid)
     */
     struct task_struct __rcu *real_parent; /* real parent process */
     struct task_struct __rcu *parent; /* recipient of SIGCHLD, wait4() reports */
     /*
     * children/sibling forms the list of my natural children
     */
     struct list_head children;     /* list of my children */
     struct list_head sibling;     /* linkage in my parent's children list */
     struct task_struct *group_leader;     /* threadgroup leader */

     /*
     * ptraced is the list of tasks this task is using ptrace on.
     * This includes both natural children and PTRACE_ATTACH targets.
     * p->ptrace_entry is p's link on the p->parent->ptraced list.
     */
     struct list_head ptraced;
     struct list_head ptrace_entry;

     /* PID/PID hash table linkage. */
     struct pid_link pids[PIDTYPE_MAX];
     struct list_head thread_group;
     struct list_head thread_node;

     struct completion *vfork_done;          /* for vfork() */
     int __user *set_child_tid;          /* CLONE_CHILD_SETTID */
     int __user *clear_child_tid;          /* CLONE_CHILD_CLEARTID */

     cputime_t utime, stime, utimescaled, stimescaled;
     cputime_t gtime;
     struct prev_cputime prev_cputime;
	#ifdef CONFIG_VIRT_CPU_ACCOUNTING_GEN
     seqlock_t vtime_seqlock;
     unsigned long long vtime_snap;
     enum {
          VTIME_SLEEPING = 0,
          VTIME_USER,
          VTIME_SYS,
     } vtime_snap_whence;
	#endif
     unsigned long nvcsw, nivcsw; /* context switch counts */
     u64 start_time;          /* monotonic time in nsec */
     u64 real_start_time;     /* boot based time in nsec */
