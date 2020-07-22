# Embeded-System-Design Lab2

Lab work of HDU-ITMO Embeded System Design

### Our Task

1. Read the FreeRTOS and Git documentation
2. Install FreeRTOS using Stm32CubeIDE
3. Create a project
4. Create two tasks
5. From each task, control the LEDs at different frequencies
6. Put the source code of the project on GitHub (2 points). You can use for this git, SourceTree, plugin for Eclipse, etc...
7. Read the FreeRTOS documentation and answer the questions in the end of this document (2 points per question)

### Steps

1. **Step two task for our project**

   ![image-20200622103047569](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg0v0h0yj2j30hg04b779.jpg)

2. **Programme the code we need**

   In the default task, we control the LED1 in `void StartDefaultTask(void *argument)`

   ```c
   void StartDefaultTask(void *argument)
   {
     for(;;)
     {
       HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_7);
       osDelay(500);
     }
   }
   ```

   In the second task, we control the LED2 in `void StartTask02(void *argument)`

   ```c
   void StartTask02(void *argument)
   {
     for(;;)
     {
       HAL_GPIO_TogglePin(GPIOB, GPIO_PIN_14);
       osDelay(1000);
     }
   }
   ```

   Then we complie and run the code , 

### Question

1. What features of FreeRTOS do you remember?
   - FreeRTOS is a mini real-time operating system kernel. As a lightweight operating system, its functions include: task management, time management, semaphore, message queue, memory management, recording function, software timer, coroutine, etc., which can basically meet the needs of smaller systems.
2. What are the differences between FreeRTOS, OpenRTOS and SafeRTOS?
   - **OpenRTOS** provides a commercial license for FreeRTOS. This includes a license for the FreeRTOS kernel as well as, if needed, the additional software libraries that make up FreeRTOS.
   - **SafeRTOS** is based on the functional model of the FreeRTOS kernel. However, SAFERTOS is not the FreeRTOS kernel. It has been completely redesigned by our team of safety experts. Our engineers took the FreeRTOS kernel functional model, subjected it to a full HAZOP, identified all areas of weakness within the functional model and API, and generated a set of safety requirements. The resulting functional and safety requirements sets were put through an IEC 61508-3 SIL 3 development life cycle, the highest possible for a software only component, creating the SAFERTOS code base and DAP.
3. Why do we need the `vTaskStartScheduler()` function?
   - It is a function starts the FreeRTOS scheduler running. Typically, before the scheduler has been started, `main()` (or a function called by `main()`) will be executing. After the scheduler has been started, only tasks and interrupts will ever execute.Starting the scheduler causes the highest priority task that was created while the scheduler was in the Initialization state to enter the Running state.
4. Why do we need the `xTaskCreate()` function?
   - This function creates a new instance of a task. Each task requires RAM that is used to hold the task state (the task control block, or TCB), and used by the task as its stack. If a task is created using `xTaskCreate()` then the required RAM is automatically allocated from the FreeRTOS heap. If a task is created using `xTaskCreateStatic()` then the RAM is provided by the application writer, which results in two additional function parameters, but allows the RAM to be statically allocated at compile time. Newly created tasks are initially placed in the Ready state, but will immediately become the Running state task if there are no higher priority tasks that are able to run. Tasks can be created both before and after the scheduler has been started.

