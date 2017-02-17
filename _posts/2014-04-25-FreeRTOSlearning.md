---
layout: posts
title: Have a close look at FreeRTOS
author: huntershuai
tags:
- OS
---


FreeRTOS learning 
===

##Framework
Including：
task、communication（queue）、hardware whisperer（port)
task.c、port.c、queue.c

<img source="/images/freertos_learning/code_organization.png"/>


Ranging from single CPU to highly functional multicore beast with *TCP/IP*,*filse system* and *USB*. FreeRTOS can be configed in the `FreeRTOSConfig.h`, for example:

	#define configMAX_PRIORITIES      ( ( unsigned portBASE_     TYPE ) 5 )
	#define configCPU_CLOCK_HZ        ( 12000000UL )
	#define configTICK_RATE_HZ        ( ( portTickType ) 1000 )
	#define configMINIMAL_STACK_SIZE  ( ( unsigned short ) 100 )
	#define configTOTAL_HEAP_SIZE     ( ( size_t ) ( 4 * 1024 ) )

  
**Hard ware configuration**

In FreeRTOS/Source/portable/IAR(tool name)/ARM_CM3(target chip)/

In `portmacro.h` defines

	#define portENTER_CRITICAL()   vPortEnterCritical()

and defines

	#define portBASE_TYPE  long              // Basic integer variable type
	#define portSTACK_TYPE unsigned long     // Pointers to memory locations
	typedef unsigned portLONG portTickType;  // The system timer tick type



In `port.c`
	
	implementation:
	vPortEnterCritical() 
	{
		...
	}



## Task Scheduling

**Task control block**

Task control block is the basic unit of Task, it describes the main attributes of one Task.

	typedef struct tskTaskControlBlock
	{
	  volatile portSTACK_TYPE *pxTopOfStack;                  /* Points to the location of
	                                                             the last item placed on 
	                                                             the tasks stack.  THIS 
	                                                             MUST BE THE FIRST MEMBER 
	                                                             OF THE STRUCT. */
	                                                         
	  xListItem    xGenericListItem;                          /* List item used to place 
	                                                             the TCB in ready and 
	                                                             blocked queues. */
	  xListItem    xEventListItem;                            /* List item used to place 
	                                                             the TCB in event lists.*/
	  unsigned portBASE_TYPE uxPriority;                      /* The priority of the task
	                                                             where 0 is the lowest 
	                                                             priority. */
	  portSTACK_TYPE *pxStack;                                /* Points to the start of 
	                                                             the stack. */
	  signed char    pcTaskName[ configMAX_TASK_NAME_LEN ];   /* Descriptive name given 
	                                                             to the task when created.
	                                                             Facilitates debugging 
	                                                             only. */
	
	  #if ( portSTACK_GROWTH > 0 )
	    portSTACK_TYPE *pxEndOfStack;                         /* Used for stack overflow 
	                                                             checking on architectures
	                                                             where the stack grows up
	                                                             from low memory. */
	  #endif
	
	  #if ( configUSE_MUTEXES == 1 )
	    unsigned portBASE_TYPE uxBasePriority;                /* The priority last 
	                                                             assigned to the task - 
	                                                             used by the priority 
	                                                             inheritance mechanism. */
	  #endif
	
	} tskTCB;

- pxTopOfStack:top address of task stack
- pxStack:start address of task stack
- uxPriority&uxBasePriority: priority index
- xGenericListItem & xEventListItem: smarter the pointer when operated among lists.

Task is created by using `xTaskCreate()` function,  the basic Task attributes are stored in the newly allocated TCB(memory), and a stack is created to store the task switch register value.
here is  ARM Cortex-M0:
	
	ARM Cortex-M0:
	StackType_t *pxPortInitialiseStack( StackType_t *pxTopOfStack, TaskFunction_t pxCode, void *pvParameters )
	{
		/* Simulate the stack frame as it would be created by a context switch
		interrupt. */
		pxTopOfStack--; /* Offset added to account for the way the MCU uses the stack on entry/exit of interrupts. */
		*pxTopOfStack = portINITIAL_XPSR;	/* xPSR */
		pxTopOfStack--;
		*pxTopOfStack = ( StackType_t ) pxCode;	/* PC */
		pxTopOfStack--;
		*pxTopOfStack = ( StackType_t ) prvTaskExitError;	/* LR */
		pxTopOfStack -= 5;	/* R12, R3, R2 and R1. */
		*pxTopOfStack = ( StackType_t ) pvParameters;	/* R0 */
		pxTopOfStack -= 8; /* R11..R4. */
	
		return pxTopOfStack;
	}


##List

Meanwhile, the TCBs are organized as List,including Ready,Pending,Delayed List

	/* Lists for ready and blocked tasks. --------------------*/
	PRIVILEGED_DATA static List_t pxReadyTasksLists[ configMAX_PRIORITIES ];/*< Prioritised ready tasks. */
	PRIVILEGED_DATA static List_t xDelayedTaskList1;						/*< Delayed tasks. */
	PRIVILEGED_DATA static List_t xDelayedTaskList2;						/*< Delayed tasks (two lists are used - one for delays that have overflowed the current tick count. */
	PRIVILEGED_DATA static List_t * volatile pxDelayedTaskList;				/*< Points to the delayed task list currently being used. */
	PRIVILEGED_DATA static List_t * volatile pxOverflowDelayedTaskList;		/*< Points to the delayed task list currently being used to hold tasks that have overflowed the current tick count. */
	PRIVILEGED_DATA static List_t xPendingReadyList;						/*< Tasks that have been readied while the scheduler was suspended.  They will be moved to the ready list when the scheduler is resumed. */


`pxReadyTasksLists[ configMAX_PRIORITIES ]`is a list array,Tasks with the same priority share
the common index in the array.


<img src="/images/freertos_learning/basic_ready_list.png" alt="ready list"/>

The listItem structure is as following :

	struct xLIST_ITEM
	{
	  portTickType xItemValue;                   /* The value being listed.  In most cases
	                                                this is used to sort the list in 
	                                                descending order. */
	  volatile struct xLIST_ITEM * pxNext;       /* Pointer to the next xListItem in the 
	                                                list.  */
	  volatile struct xLIST_ITEM * pxPrevious;   /* Pointer to the previous xListItem in 
	                                                the list. */
	  void * pvOwner;                            /* Pointer to the object (normally a TCB)
	                                                that contains the list item.  There is
	                                                therefore a two-way link between the 
	                                                object containing the list item and 
	                                                the list item itself. */
	  void * pvContainer;                        /* Pointer to the list in which this list
	                                                item is placed (if any). */
	};

and list structure:

	typedef struct xLIST
	{
	  volatile unsigned portBASE_TYPE uxNumberOfItems;
	  volatile xListItem * pxIndex;           /* Used to walk through the list.  Points to
	                                             the last item returned by a call to 
	                                             pvListGetOwnerOfNextEntry (). */
	  volatile xMiniListItem xListEnd;        /* List item that contains the maximum 
	                                             possible item value, meaning it is always
	                                             at the end of the list and is therefore 
	                                             used as a marker. */
	} xList;

the listItem in the list are sorted in high to low sequence, which means the first item is the `xListend` with the highes value(0xFFFF or 0xffffffff), plays the role of list marker.



<img src="/images/freertos_learning/ready_list_full.png" alt="ready list full"/>






##Queue
Queue structure:

	typedef struct QueueDefinition
	{
	  signed char *pcHead;                      /* Points to the beginning of the queue 
	                                               storage area. */
	  signed char *pcTail;                      /* Points to the byte at the end of the 
	                                               queue storage area. One more byte is 
	                                               allocated than necessary to store the 
	                                             queue items; this is used as a marker. */
	  signed char *pcWriteTo;                   /* Points to the free next place in the 
	                                               storage area. */
	  signed char *pcReadFrom;                  /* Points to the last place that a queued 
	                                               item was read from. */
	                                           
	  xList xTasksWaitingToSend;                /* List of tasks that are blocked waiting 
	                                               to post onto this queue.  Stored in 
	                                               priority order. */
	  xList xTasksWaitingToReceive;             /* List of tasks that are blocked waiting 
	                                               to read from this queue. Stored in 
	                                               priority order. */
	
	  volatile unsigned portBASE_TYPE uxMessagesWaiting;  /* The number of items currently
	                                                         in the queue. */
	  unsigned portBASE_TYPE uxLength;                    /* The length of the queue 
	                                                         defined as the number of 
	                                                         items it will hold, not the 
	                                                         number of bytes. */
	  unsigned portBASE_TYPE uxItemSize;                  /* The size of each items that 
	                                                         the queue will hold. */
	                                         
	} xQUEUE;


##Task Scheduling Process
**Note:**Task Block and Task stack can be locate in non-continuous memory.
<img src="/images/freertos_learning/task_stack.png" alt="task_stack"/>

when task is created:

<img src="/images/freertos_learning/stack_state.png" alt="stack_state"/>

	StackType_t *pxPortInitialiseStack( StackType_t *pxTopOfStack, TaskFunction_t pxCode, void *pvParameters )
	{
		/* Simulate the stack frame as it would be created by a context switch
		interrupt. */
		pxTopOfStack--; /* Offset added to account for the way the MCU uses the stack on entry/exit of interrupts. */
		*pxTopOfStack = portINITIAL_XPSR;	/* xPSR */
		pxTopOfStack--;
		*pxTopOfStack = ( StackType_t ) pxCode;	/* PC */
		pxTopOfStack--;
		*pxTopOfStack = ( StackType_t ) prvTaskExitError;	/* LR */
		pxTopOfStack -= 5;	/* R12, R3, R2 and R1. */
		*pxTopOfStack = ( StackType_t ) pvParameters;	/* R0 */
		pxTopOfStack -= 8; /* R11..R4. */
	
		return pxTopOfStack;
	}

when the first task is scheduled:	
	__asm void prvPortStartFirstTask( void )
	{
		extern pxCurrentTCB;
	
		PRESERVE8
	
		/* The MSP stack is not reset as, unlike on M3/4 parts, there is no vector
		table offset register that can be used to locate the initial stack value.
		Not all M0 parts have the application vector table at address 0. */
	
		ldr	r3, =pxCurrentTCB	/* Obtain location of pxCurrentTCB. */
		ldr r1, [r3]
		ldr r0, [r1]			/* The first item in pxCurrentTCB is the task top of stack. */
		adds r0, #32			/* Discard everything up to r0. */
		msr psp, r0				/* This is now the new top of stack to use in the task. */
		movs r0, #2				/* Switch to the psp stack. */
		msr CONTROL, r0			/* Config `CONTROL` register to PSP & privilege mode*/	
		pop {r0-r5}				/* Pop the registers that are saved automatically. */
		mov lr, r5				/* lr is now in r5. */
		cpsie i					/* The first task has its context and interrupts can be enabled. */
		pop {pc}				/* Finally, pop the PC to jump to the user defined task code. */
	
		ALIGN
	}

task switch process happened in pendsv handler, first restore task1 stack, and switch to task2 stack and pop register of task2:

	__asm void xPortPendSVHandler( void )
	{
		extern vTaskSwitchContext
		extern pxCurrentTCB
	
		PRESERVE8
	
		mrs r0, psp
	
		ldr	r3, =pxCurrentTCB 	/* Get the location of the current TCB. */
		ldr	r2, [r3]
	
		subs r0, #32			/* Make space for the remaining low registers. */
		str r0, [r2]			/* Save the new top of stack. */
		stmia r0!, {r4-r7}		/* Store the low registers that are not saved automatically. */
		mov r4, r8				/* Store the high registers. */
		mov r5, r9
		mov r6, r10
		mov r7, r11
		stmia r0!, {r4-r7}
	
		push {r3, r14}
		cpsid i
		bl vTaskSwitchContext
		cpsie i
		pop {r2, r3}			/* lr goes in r3. r2 now holds tcb pointer. */
	
		ldr r1, [r2]
		ldr r0, [r1]			/* The first item in pxCurrentTCB is the task top of stack. */
		adds r0, #16			/* Move to the high registers. */
		ldmia r0!, {r4-r7}		/* Pop the high registers. */
		mov r8, r4
		mov r9, r5
		mov r10, r6
		mov r11, r7
	
		msr psp, r0				/* Remember the new top of stack for the task. */
	
		subs r0, #32			/* Go back for the low registers that are not automatically restored. */
		ldmia r0!, {r4-r7}      /* Pop low registers.  */
	
		bx r3
		ALIGN
	}


