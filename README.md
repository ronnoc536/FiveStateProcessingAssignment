# FiveStateProcessingAssignment

Description
In this assignment you will implement a system to emulate process execution in a five-state process model. You will be provided with a code base that manages processes being added to the emulation system and handles IO requests. 
The code base is structured to run a loop; at the top of this loop, a time step variable is incremented, the process set is updated with new processes, and active IO requests are managed (this is all given to you in the code base). After that, you will add your code to manage the processes! At each time step (i.e., loop iteration) only one action can be taken. Here is how it should work:

If there IS a process currently running (i.e., if there was a process running at the end of the last time step):
Continue running it, checking if you need to issue IO requests or if the process is complete (more on that below). Issuing an IO request for the running process counts as an action for the time step. Similarly, completing a running process counts as an action.
This process should continue to run until either it completes or is blocked, due to an IO request. Just letting the running process keep running counts as an action.
If there IS NOT a process running (i.e., if there was not a process running at the end of the last time step):
Do one of the following actions (in order of precedence, doing one of these is an action for the step):
If a process has been added to the emulation system for you to manage (i.e., added to the processList variable, it will have its "state" member variable set to newArrival), admit this process to the system (i.e., add it to the "ready list").  NOTE: only do this for one newArrival process
If there is an IO interrupt that has been raised (i.e., added to the interrupts variable), respond to it (i.e., update the state for the process that issued the interrupt).  NOTE: do this for only one interrupt
Select a new process to run from the set of ready processes (in first in first out fashion). This is only selected if the above two options do not apply.
So, all of your code should be in main.cpp (where this loop is), but if you feel you really need to update some of the supporting files, reach out to your professor and see what they think.

 

More Details:
Adding code to an existing code base can be a little overwhelming, but don't stress! We will introduce the code base in class and discuss some there. Also, you should spend some time looking it over on your own and make sure to ask us if you don't follow how the provided code works! There are two locations where you will need to add code in main (indicated by a TODO comment), be sure to locate these; you can add code elsewhere, but these spots have to be updated.

Once you have a feel for what we gave you, sketch out some ideas on how you want to tackle the problem. Here are some details on the individual programming tasks you will need to undertake to do this assignment:

Admitting new processes and labeling them as ready to run (new -> ready transition)
The code base includes a class called ProcessManagement. When the program starts, it will read an input file that will provide details for the set of processes to run (this functionality is built into the code base). Each line in this input file indicates the following details for a process (in this order):

<Arrival Time> <Required CPU Time> [<IO Event time> <IO Event Duration>]*
The arrival time indicates when a process will be added to the set of processes that you will be managing (these will appear in the processList variable in main automatically for you at the indicated time step).
The required CPU time indicates how long each process should be on the CPU
And finally, each process may have zero or more (blocking) IO events that occur during their execution. These are defined by the time into the process execution that they arrive (IO Event Time) and how long they take (IO Event Duration). So, if a process starts running at time step 5 and has an IO event with time value of 3 and duration of 10, then the process will make an IO request 3 time units into its run (time step 8), which will be done in 10 time steps (time step 18). At time step 18, the IOModule (built into the code base) will issue an IOInterrupt that you will need to respond to (more on that below).

This information will be set for each process and stored in an instance of the Process struct (defined in process.h). The IO events will be stored in order of time that they occur, for your convenience (so the first event will be at the head of the ioEvents list.
A default process file is provided (processList.txt) and some other examples are included. Feel free to make your own for testing, too.

Selecting an appropriate process to run from the set of ready processes (we will just use a first in, first out scheduler -- ready -> running transition)
In the code base, there is a variable called processList that is of type list<Process>. You can manipulate processes that show up in this list in whatever way you see fit to manage the processes (e.g., create other lists to hold processes in different states, create references into this list, or just manage the processes in processList directly).
Regardless of the method you use, you will need to be able to select the process to run next in a first-in, first-out fashion when appropriate. This means that the first process to hit the "ready list" (not necessarily a list, though), should be the first one to get selected to run (i.e., should work just like a normal waiting line).

Handling running processes and what can happen while a process is running (running -> blocked and running -> exit transitions)
When a process is running, you should update the amount of time the process has spent on the processor and then check for either of the two following cases:
An IO request may need to be made (as indicated by the running process' IO event list). To do this, you will submit and IO request to the ioModule object (already in the base code). If our current running process is p and our current time is t, then this call will look like:
ioModule.submitRequest(t, p.ioEvents.front(), p);
At this point, the process will be waiting for the IO event to complete and should not be run until you have been notified that the IO is done (more on that below).
The process may be finished running. At this point, you need to mark the process as being done and make sure not to schedule it any more time on the processor.

Responding to interrupts that are raised when an IO operation is complete (blocked -> ready transition)
In the base code, there is a variable called interrupts in main that is of type list<IOInterrupt>. The ioModule class will automatically add IOInterrupt objects to this list at the appropriate time step after an IO request has been made (see above). The IOInterrupt object is simple, just indicating the ioEventID and procID associated with the interrupt (each process and IO event has a unique ID, so you can use one or both of these to find the process whose request is now complete). You will need to take this information and make it so the indicated process is marked as ready to run again and clear the interrupt out of the list (this can be done a basic pop_front function call on this list after you are done with the interrupt).
 

Compiling and running the code base

The code base includes a makefile. For those that are new to Makefiles, they are a tool to compile code on Linux systems. To use them, put all of your code into a directory (on linux), then navigate to that directory in a terminal and enter make and hit enter. This will compile your code and (if successful) create an executable called program in that directory. 

To run the program you can just enter ./program and press enter, which will run using the provided processList.txt as input. You can also enter
./program [file name]
which will run the program using the indicated file as input. Or enter
./program [file name] [sleep time]
which will run the program using the indicated file as input and will sleep between time steps the indicated amount in milliseconds (the default is 50).

 

What to Submit

When you are done, add all of your code files (just .h and .cpp, no executables) and the makefile to a zip archive and upload that to Canvas.

 

Grading

Your code does not need to be perfect; it does not need to be extremely space efficient or fast. Your code does need to be readable, commented where necessary, formatted consistently, and able to compile on at least 1 campus machine.
We will expect your output to follow the output scheme that is implemented in the code base when you get it; if you want to modify/add to the output, you can, just make sure to revert it back to what was in the code base to start with.
(hint: diff is a great tool for comparing output, if your output doesn't match ours due to whitespace differences or something like that, it will make the grader very grumpy, which you do not want. Make it match!).
 

Rubric:
Clean compilation - 30 pts
Code quality - 10 pts
Correct time step action selection - 10 pts
Correct management of IO/Blocked processes - 15 pts
Correct management of Ready processes - 15 pts
All processes run to completion - 10 pts
Test Input - 10 pts
