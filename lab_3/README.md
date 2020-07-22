# Embeded-System-Design Lab3

Lab3 work of HDU-ITMO Embeded System Design

### Our Task

1. Read the FreeRTOS documentation about queue management

2. Create a new project

3. Create two task
4. Create one queue
5. One of the tasks flashes briefly LED when receiving information from the queue.
6. The second task writes any number to the queue and delayed 1 sec.
7. Write a report (1 point)
8. Put the entire project (src) in GitHub, use .gitignore to avoid undesirable files (1 point)
9. Read the FreeRTOS documentation and answer the questions (2 points per question) in Yandex Forms

### Steps

1. **Step two task for our project**

   ![image-20200622103047569](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg0v0h0yj2j30hg04b779.jpg)

2. **Programme the code we need**

   first of all , we need to add the dependency 

   ```c
   #include "queue.h"
   ```
   
   then we should define the Queue we need as a global variable
   
   ```c
   xQueueHandle q;
   ```
   

Create a Queue in the Main function

```c
   q = xQueueCreate(8,sizeof(unsigned int));
```

   In the default task, we end the message in `void StartDefaultTask(void *argument)`

   ```c
   void StartDefaultTask(void *argument)
   {
   	unsigned int data = 1;
   
  /* USER CODE BEGIN 5 */
     /* Infinite loop */
     for(;;)
     {
   	  xQueueSend(q, (void *) &data, portMAX_DELAY);
   	  osDelay(data*100);
   	  data++;
   
     }
     /* USER CODE END 5 */
   
   }
   ```

   In the second task, we receive the message and control the LED when `StartTask02` receive the message.

   ```c
   void StartTask02(void *argument)
   {
   	int rec;
   	  /* USER CODE BEGIN StartTask02 */
   	  /* Infinite loop */
   	  if(uxQueueMessagesWaiting(q)>1)
   	  {
   		 xQueueReceive(q, &(rec), portMAX_DELAY);
   		 for(int i = 0;i <= rec; i++){
   			 HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_14);
   			 osDelay(100);
   		 }
   	  }
   	  /* USER CODE END StartTask02 */
   
   }
   ```

   Then we complie and run the code , 

### Summary

In this experiment, I learned how to use the queue API, and realized the communication between thread tasks, and then control the led



