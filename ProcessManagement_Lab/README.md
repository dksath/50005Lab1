 50.005 Lab 1 Process Management Lab

*TODO#4: Implement​ main_loop() busy wait for job creation and termination plus explaining the worker process revival process*

## Main Loop: Updating and Dispatching tasks to the worker processes
My way is technically the way the document has recommended us to implement *main_loop()*, update worker processes with new tasks and constantly assign new ones if they are still alive and after they become available again to do new tasks. If any of the processes get terminated prematurely, spawn a new worker process and then dispatch a new job onto it. My psuedocode below will explain it a bit more in depth.

### Pseudocode of main_loop()
```
1   while busy waiting,
2       if child process[i] is alive & available
3           update child process[i]'s job buffer with a new task
4           notify child process i with sem_post
5           indicate doing_task when job is dispatched
6       else
7           if child process is not alive and its status == 9 (meaning it has been prematurely terminated)
8               spawn a new worker process
9               assign the child process[i] and pid that was prematurely terminated to the new worker process
10              update child process[i]'s job buffer with a new task
11              notify child process i with sem_post
12              indicate doing_task when job is dispatched

13      if at any point doing_task is being indicated when a job is dispatched 
14          break   
```

## Main Loop (II): Terminating all child processes with 'z' tasks
After all tasks in the input file have been done by exiting the previous loop, the tasks will enter a second busy waiting loop whereby the main process will send termination signals to all child processes using the 'z' task that are still alive. Those that got prematurely terminated get revived in the previous busy wait loop therefore all child processes should be alive again before entering the second loop. Check from the for loop if any child processes are still alive, if their task status is zero, update its task status to 1 and post the 'z' task to it. Update the termination number and once the termination number equals the number of processes first called, *main_loop()* can exit the second loop. My psuedocode below will explain it a bit more in depth.

### Pseudocode
```
1    while busy waiting,
2       initialize termination number
3       for i = 0 up to the number of processes
4           if child process[i] still alive
5               if child process[i]'s task status is 0
6                   set child process[i]'s task status to 1
7                   update child process[i]'s job buffer with a 'z' task
8                   notify child process[i] with sem_post
9                   update termination number
10      if termination number == number of processes
11          break
```