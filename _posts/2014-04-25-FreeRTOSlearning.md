---
layout: posts
title: Have a close look at FreeRTOS
author: huntershuai
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

