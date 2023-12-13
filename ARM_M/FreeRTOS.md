设置软件仿真

修改时钟大小

添加文件路径

#### 多任务系统

实时响应事件，实时处理事件

### 数据结构

列表，列表项(节点)

```c
#include "FreeRTOS.h"
#include "list.h"
#include <stdlib.h>

/* 节点初始化 */
void vListInitialiseItem( ListItem_t * const pxItem )
{
	/* 初始化该节点所在的链表为空，表示节点还没有插入任何链表 */
	pxItem->pvContainer = NULL;
}

/* 链表根节点初始化 */
void vListInitialise( List_t * const pxList )
{
	/* 将链表索引指针指向尾节点地址 */
    pxList->pxIndex = (ListItem_t *) & pxList->xListEnd;
	/* 将尾节点的链表节点辅助值设为最大值 */
	pxList->xListEnd.xItemValue = portMAX_DELAY;
	/* 将尾节点的前后指针都指向其自己，故使链表结构为一，即->数据空 */
	pxList->xListEnd.pxNext = (ListItem_t *) & pxList->xListEnd;
	pxList->xListEnd.pxPrevious = (ListItem_t *) & pxList->xListEnd;
	/* 将链表节点技术器的值置 0 代表链表为空 */
	pxList->uxNumberOfItems = (UBaseType_t) 0U;
}

/* 将节点插入到链表的尾部 */
void vListInsertEnd( List_t * const pxList, ListItem_t * const pxNewListItem )
{
	/* 将尾根节点的索引指针以节点形式取出 */
	ListItem_t * const pxIndex = pxList->pxIndex;
	/* 将新节点的后指针与根节点的索引指针指向同一方向，即为根节点 */
	pxNewListItem->pxNext = pxIndex;
	/* 新节点的前指针与根节点的前指针指向同一节点，即之前根节点的上个节点 */
	pxNewListItem->pxPrevious = pxIndex->pxPrevious;
	/* 根节点的前指针节点的后指针指向新节点 */
	pxIndex->pxPrevious->pxNext = pxNewListItem;
	/* 根节点的前指针为新节点 */
	pxIndex->pxPrevious = pxNewListItem;

	/* 记住该节点所在的链表 */
	pxNewListItem->pvContainer = ( void * ) pxList;

	/* 链表节点计数器++ */
	(pxList->uxNumberOfItems)++;

}


/* 将节点按照升序排列插入到链表 */
void vListInsert( List_t * const pxList, ListItem_t * const pxNewListItem )
{
	ListItem_t * pxIterator;
	
	/* 获取节点的排序辅助值 */
	const TickType_t xValueOfInsertion = pxNewListItem->xItemValue;

	/* 寻找节点要插入的位置 */
	if( xValueOfInsertion == portMAX_DELAY )
	{
		pxIterator = pxList->xListEnd.pxPrevious;
	}
	else
	{
		for( pxIterator = ( ListItem_t * ) &( pxList->xListEnd );
		     pxIterator->pxNext->xItemValue <= xValueOfInsertion; 
			 pxIterator = pxIterator->pxNext )
		{
			/* 没有事情可做，不断迭代只为了找到节点要插入的位置 */			
		}
	}

	pxNewListItem->pxNext = pxIterator->pxNext;
	pxNewListItem->pxNext->pxPrevious = pxNewListItem;
	pxNewListItem->pxPrevious = pxIterator;
	pxIterator->pxNext = pxNewListItem;

	/* 记住该节点所在的链表 */
	pxNewListItem->pvContainer = ( void * ) pxList;

	/* 链表节点计数器++ */
	( pxList->uxNumberOfItems )++;
}



/* 将节点从链表中删除 */
UBaseType_t uxListRemove( ListItem_t * const pxItemToRemove )
{
	/* 获取节点所在的链表 */
	List_t * const pxList = ( List_t * ) pxItemToRemove->pvContainer;

	pxItemToRemove->pxNext->pxPrevious = pxItemToRemove->pxPrevious;
	pxItemToRemove->pxPrevious->pxNext = pxItemToRemove->pxNext;

	/* Make sure the index is left pointing to a valid item. */
	if( pxList->pxIndex == pxItemToRemove )
	{
		pxList->pxIndex = pxItemToRemove->pxPrevious;
	}

	/* 初始化该节点所在的链表为空，表示节点还没有插入任何链表 */
	pxItemToRemove->pvContainer = NULL;
	
	/* 链表节点计数器-- */
	( pxList->uxNumberOfItems )--;

	/* 返回链表中剩余节点的个数 */
	return pxList->uxNumberOfItems;
}

```

#### 实现就序列表

就绪列表实际上就是一个List_t类型的数组，数组的大小由决定最大任务优先级的宏configMAX_PRIORITIES决定，目前我们将configMAX_PRIORITIES在FreeRTOSConfig.h中默认定义为5，最大支持256个优先级。数组的下标对应了任务的优先级，同一优先级的任务统一插入到就绪列表的同一条链表中。

```
1.定义就序列表
2.就序列表初始化
3.将任务插入到就序列表
```

#### 实现调度器

调度器是操作系统的核心，其主要功能就是实现任务的切换，即从就绪列表里面找到优先级最高的任务，然后去执行该任务。

调度器的启动由vTaskStartScheduler()函数来完成，该函数在task.c中。

指定运行的任务，然后调用xPortStartScheduler()函数

![image-20230807175939398](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20230807175939398.png)

​		SVC中断要想被成功响应，其函数名必须与向量表注册的名称一致SVC_Handler ，我们在FreeRTOSConfig.h中添加添加宏定义的方法来修改vPortSVCHandler()。

​		当从SVC中断服务退出前，通过向r14寄存器最后4位按位或上0x0D，使得硬件在退出时使用进程堆栈指针PSP完成出栈操作并返回后进入任务模式、返回Thumb状态。在SVC中断服务里面，使用的是MSP堆栈指针。 

​		当r14的bit0为1表示返回thumb状态，bit1和bit2分别表示返回后sp用msp还是psp以及返回到特权模式还是用户模式。

##### 任务切换

​		任务切换就是在就绪列表中寻找优先级最高的就绪任务，然后去执行该任务。但是目前我们还不支持优先级，仅实现两个任务轮流切换。

​		任务切换函数：taskYIELD()。portYIELD的实现很简单，实际就是将PendSV的悬起位置1，当没有其它中断运行的时候响应PendSV中断，去执行我们写好的PendSV中断服务函数，在里面实现任务切换。

​		通过宏定义将xPortPendSVHandler()定义为PendSV。

​		上文保存：将PSP的值存储到r0。当进入PendSVC Handler时，上一个任务运行的环境即： xPSR，PC（任务入口地址），R14，R12，R3，R2，R1，R0（任务的形参）这些CPU寄存器的值会自动存储到任务的栈中，剩下的r4~r11需要手动保存，同时PSP会自动更新（在更新之前PSP指向任务栈的栈顶）。

​		以r0作为基址，指针先递减，再操作，将CPU寄存器r4~r11的值存储到任务栈，同时更新r0的值。

#### 临界段

​		临界段用一句话概括就是一段在执行的时候不能被中断的代码段。在 FreeRTOS 里面， 这个临界段最常出现的就是对全局变量的操作，全局变量就好像是一个枪把子，谁都可以 对他开枪，但是我开枪的时候，你就不能开枪，否则就不知道是谁命中了靶子。可能有人 会说我可以在子弹上面做个标记，我说你能不能不要瞎扯淡。 

​		那么什么情况下临界段会被打断？一个是系统调度，还有一个就是外部中断。在 FreeRTOS，系统调度，最终也是产生 PendSV 中断，在 PendSV Handler 里面实现任务的切 换，所以还是可以归结为中断。既然这样，FreeRTOS 对临界段的保护最终还是回到对中断 的开和关的控制。

​		FreeRTOS 关中断的函数在 portmacro.h 中定义，分不带返回值和带返回值两种；

```c
 /* 不带返回值的关中断函数，不能嵌套，不能在中断里面使用 */ 
 #define portDISABLE_INTERRUPTS() vPortRaiseBASEPRI()
 
 void vPortRaiseBASEPRI( void )
 {
 	uint32_t ulNewBASEPRI = configMAX_SYSCALL_INTERRUPT_PRIORITY; 
 	__asm
 	{
 		msr basepri, ulNewBASEPRI 
 		dsb
 		isb
 	}
 }
 
 /* 带返回值的关中断函数，可以嵌套，可以在中断里面使用 */ 
 #define portSET_INTERRUPT_MASK_FROM_ISR() ulPortRaiseBASEPRI()
 ulPortRaiseBASEPRI( void )
 {
 	uint32_t ulReturn, ulNewBASEPRI = configMAX_SYSCALL_INTERRUPT_PRIORITY; 
 	__asm
 	{
 		mrs ulReturn, basepri 
 		msr basepri, ulNewBASEPRI 
 		dsb
 		isb
 	}
 	return ulReturn; 
 }
```

​		在 FreeRTOS 中，对临界段的保护出现在两种场合，一种是在中断场合一种是在非中 断场合，具体的应用见。

### 空闲任务与阻塞延时

​		系统必须保证每时每刻都有任务在运行，使用 RTOS 的很大优势就是榨干 CPU 的性能，永远不能让它闲着，任务如果需要延 时也就不能再让 CPU 空等来实现延时的效果。RTOS 中的延时叫阻塞延时，即任务需要延 时的时候，任务会放弃 CPU 的使用权，CPU 可以去干其它的事情，当任务延时时间到，重 新获取 CPU 使用权，任务继续运行，这样就充分地利用了 CPU 的资源，而不是干等着。

 		当任务需要延时，进入阻塞状态，那 CPU 又去干什么事情了？如果没有其它任务可以 运行，RTOS 都会为 CPU 创建一个空闲任务，这个时候 CPU 就运行空闲任务。在 FreeRTOS 中，空闲任务是系统在【启动调度器】的时候创建的优先级最低的任务，空闲任 务主体主要是做一些系统内存的清理工作。但是为了简单起见，我们本章实现的空闲任务 只是对一个全局变量进行计数。鉴于空闲任务的这种特性，在实际应用中，当系统进入空 闲任务的时候，可在空闲任务中让单片机进入休眠或者低功耗等操作。

### 支持多优先级

### 任务延时列表的实现

#### 创建多任务--SRAM动态内存

#### 任务状态

1.   就绪：该任务在就绪列表中，就绪的任务已经具备执行的能力，只等待调度器进行调度，新创建的任务会初始化为就绪态。
2.   运行：该状态表明任务正在执行，此时它占用处理器，FreeRTOS调度器选择运行的永远是处于最高优先级的就绪态任务，当任务被运行的一刻，它的任务状态就变成了运行态
3.   阻塞：如果任务当前正在等待某个时序或外部中断，我们就说这个任务处于阻塞状态，该任务不在就绪列表中。包含任务被延时、任务正在等待信号量、读写队列或者等待读写事件等。
4.   挂起：处于挂起态的任务对调度器而言是不可见的，让一个任务进入挂起状态的唯一办法就是调用 vTaskSuspend()函数；而 把 一 个 挂 起 状态 的 任 务 恢复的 唯 一 途 径 就 是 调 用 vTaskResume() 将任务从挂起列表中删除并插入到就绪列表中；或vTaskResumeFromISR()函数。

![image-20230808194457160](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20230808194457160.png)

## 队列

![image-20230810211053519](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20230810211053519.png)

![image-20230810211102105](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20230810211102105.png)