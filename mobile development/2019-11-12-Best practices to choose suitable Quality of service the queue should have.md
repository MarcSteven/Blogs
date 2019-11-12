# Best practices to choose suitable Quality of service the queue should have

If you just need a concurrent queue but don't want to manage your own, you can use the global class method on dispatch queue to get one of the pre-defined global queues

* .userInteractive
Recommended for tasks that the user directly interacts with, for instance UI-updating calculations, animations or anything needed to keep UI responsive and fast

* .userInitiated
“The .userInitiated queue should be used when the user kicks off a task from the UI that needs to happen immediately, but can be done asynchronously. For example, you may need to open a document or read from a local database

* .utility

You'll want to use the .utility dispatch queue for tasks that would typically include a progress indicator such as long-running computations, I/O, networking or continuous data feeds. The system tries to balance responsiveness and performance with energy efficiency. 

* .background


For tasks that the user is not directly aware of you should use the .background queue. They don't require user interaction and aren't time sensitive. Prefetching, database maintenance, synchronizing remote servers and performing backups are all great examples. The OS will focus on energy efficiency instead of speed. You'll want to use this queue for work that will take significant time, on the order of minutes or more.

* .default and .unspecified

There are two other possible choices that exist, but you should not use explicitly. There's a .default option, which falls between .userInitiated and .utility and is  the default value of the qos argument. It's not intended for you to directly use.
The second options is .unspecified.
exists to support legacy APIs that may opt the thread out of a quality of service. It's good to know they exist, but if you're using them, you're almost certainly doing something wrong.




