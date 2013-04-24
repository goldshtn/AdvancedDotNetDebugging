Advanced .NET Debugging with WinDbg and SOS
===========================================

The first demo involves taking a crash dump of a process that encounters an unhandled exception during startup. To capture the dump, use the following command:

```
procdump -e -ma -x 1_Crash\Binaries\FileExplorer.exe crash.dmp
```

Then, open the dump in Visual Studio or in WinDbg. In WinDbg, load SOS and then issue the !PrintException command to obtain a stack trace of the exception that occurred.

The second demo involves a memory leak and is somewhat more complicated to diagnose. The source of the memory leak has to do with the finalization queue: the application creates objects at a rate of 100/second, but each object's finalizer takes 20ms to run (because it calls Thread.Sleep). As a result, only 50 objects can be cleaned up per second, creating a memory leak. Some commands that are useful for the diagnosis:

```
!dumpheap -stat
.foreach (bytearr {!dumpheap -type System.Byte[] -min 500 -short}) {!gcroot bytearr; .echo --------}
!finalizequeue
```

The third demo involves a deadlock -- two threads are waiting to acquire two different synchronization objects, but each object is owned by another thread. To diagnose the situation, use the SOSEX debugging extension and its !dlk command.
