Rom code review
===

# BeeTaskInit()


OS_Timer_init()

create timer list, here timer is orgnize as following:
  	
	/* The definition of the timers themselves. */
          typedef struct tmrTimerControl
	{
    const char                *pcTimerName;        /*<< Text name.  This is not used by the kernel, it is included simply to make debugging easier. */ /*lint !e971 Unqualified char types are allowed for strings and single characters only. */
    ListItem_t                xTimerListItem;        /*<< Standard linked list item as used by all kernel features for event management. */
    TickType_t                xTimerPeriodInTicks;/*<< How quickly and often the timer expires. */
    UBaseType_t                uxAutoReload;        /*<< Set to pdTRUE if the timer should be automatically restarted once expired.  Set to pdFALSE if the timer is, in effect, a one-shot timer. */
    void                     *pvTimerID;            /*<< An ID to identify the timer.  This allows the timer to be identified when the same callback is used for multiple timers. */
    TimerCallbackFunction_t    pxCallbackFunction;    /*<< The function that will be called when the timer expires. */
	#if( configUSE_TRACE_FACILITY == 1 )
	#ifndef USING_LIST_TIMER_PROTECT
	        UBaseType_t            uxTimerNumber;        /*<< An ID assigned by trace tools such as FreeRTOS+Trace */
	#endif
	    #endif
	#ifdef USING_LIST_TIMER
	    struct tmrTimerControl *next;
	
	#ifdef USING_LIST_TIMER_PROTECT
	        uint8_t my_entry_id;
	        uint8_t rsvd[3];
	#endif
	#endif
	} xTIMER;

Timer can be created to dynamic and static,
     
```
     #define OS_MAX_TIMER_BUCKETS 0x20 //32 timer
	
     static timer list is array: timer_pool[OS_MAX_TIMER_BUCKETS] 
     ```

     dynamic timer list is created by `pvmalloc` memory:
     ```


     ```
     
