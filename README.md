# Embeded-System-Design Lab4

**Lab4 work** of HDU-ITMO Embeded System Design

**Name:** Zhou Guancheng

**Student ID:** 192050193

### My Variant : Variant3

1. Create three tasks.

2. Create two queues. The size of queues is 5 integer numbers. The first queue should be used to transmit data from Task 1 to Task 2. The second queue should be used to transmit data from Task 1 to Task 3.

3. Task 1 should increment the local integer variable “counter” once per second. Task 1 sends the “counter” value to Task 2 once per second and to Task 3 once per two seconds. The incrementing of “counter” variable should be paused if the corresponding queue is full and resumed if the corresponding queue is not full.

4. Task 2 and Task 3 should toggle LED LD3 once per 600 ms. The number of toggling is equal “counter” variable received using queue from Task 1. The LED is shared resource between Task 2 and Task 3. Task 2 or Task 3 should work with the shared LED using a mutex.

### Steps

1. **Step Three task for our project**

![image-20200622103047569](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg0v0h0yj2j30hg04b779.jpg)

2. **Programme the code we need**

first of all , we need to add the dependency 

```c
#include "queue.h"
#include "FreeRTOS.h"
#include "semphr.h"
```

then we should define the Queue and the mutex variable we need as a global variable.

```c
xQueueHandle q1;
xQueueHandle q2;
SemaphoreHandle_t xSemaphore = NULL;
```

Create a Queue and a mutex in the Main function

```c
q1 = xQueueCreate(8,sizeof(unsigned int));
q2 = xQueueCreate(8,sizeof(unsigned int));
// Create mutex
xSemaphore = xSemaphoreCreateMutex();
```

In the default task, we send the message to q1 and q2 . Every queue will be reset if it`s full before the default task send messages to it. 

   ```c
void StartDefaultTask(void *argument)
{
  int counter = 1;
  /* USER CODE BEGIN 5 */
  /* Infinite loop */

  for(int i = 0; i < 1000; i++)
  {

	 if(uxQueueSpacesAvailable(q1) == 0){
		 xQueueReset(q1);
	 }

	 if(uxQueueSpacesAvailable(q2) == 0){
		 xQueueReset(q2);
	 }

    osDelay(1000);
    counter++;
    // the maximum number of counter is 3
    counter = counter % 4;
    // send message to q1 per second
    xQueueSend(q1,(void *) &counter,portMAX_DELAY);

    if(counter % 2 != 0){
    	// send message to q2 once per two seconds
    	xQueueSend(q2,(void *) &counter,portMAX_DELAY);
    }

  }
  /* USER CODE END 5 */ 
}

   ```

In the second task, we receive the message and control the LED when `StartTask02` receive the message. When led4 is occupied, we need to use mutex to occupy resources, and then when the task is completed, we release mutex resources.

   ```c
void StartTask02(void *argument)
{
	for(;;){
		/* USER CODE BEGIN StartTask02 */
		int rec1;
		if(uxQueueMessagesWaiting(q1)>1)
		{
		 xQueueReceive(q1, &(rec1), portMAX_DELAY);
		 if(xSemaphore != NULL){
			 if(xSemaphoreTake(xSemaphore,portMAX_DELAY) == pdTRUE){
				 for(int i = 0;i <= rec1; i++){
						 HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_14);
						 osDelay(600);
					 }
				 xSemaphoreGive(xSemaphore);
			 }
		 }
		}
	}
}
   ```

In the third task, we receive the message and control the LED when `StartTask03` receive the message. When led4 is occupied, we need to use mutex to occupy resources, and then when the task is completed, we release mutex resources. 

```c
void StartTask03(void *argument)
{
  /* USER CODE BEGIN StartTask03 */
	for(;;){
		int rec2;
		if(uxQueueMessagesWaiting(q2)>1)
		{
			xQueueReceive(q2, &(rec2), portMAX_DELAY);
			if(xSemaphore != NULL){
				if(xSemaphoreTake(xSemaphore,portMAX_DELAY) == pdTRUE){
					for(int i = 0;i < rec2; i++){
							HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_14);
							osDelay(600);
					}
					xSemaphoreGive(xSemaphore);
				}
			}

		}
	}
  /* USER CODE END StartTask03 */
}
```

 Then we complie and run the code and get the correct result.

### Summary

In this experiment, I learned how to use the queue API and mutex, and realized the communication between thread tasks, and then control the led



