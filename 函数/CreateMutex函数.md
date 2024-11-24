创建或打开一个已命名或未命名的互斥对象
```
HANDLE CreateMutexW(
  [in, optional] LPSECURITY_ATTRIBUTES lpMutexAttributes,
  [in]           BOOL                  bInitialOwner,
  [in, optional] LPCWSTR               lpName
);
```


lpMutexAttributes [in, optional]
指向SECURITY_ ATTRIBUTES结构的指针。如果此参数力NULL,则该句柄不能由子进
程继承。
blnitialOwner [in]
如果此值为TRUE 并且调用者创建了互斥锁，则调用线程将荻得互斥锁对象的初始所有权。
否则，调用线程不会荻得互斥锁的所有权。
l pName [in, o ptional]
互斥对象的名称。该名称仅限于MAX_PATH宇符，名称区分大小写。如果lpName 为N切上，
则会创建不带名称的互斥对象。
如果lpName与现有事件、信号量、等待定时器、作业或文件映射对象的名称匹配，且这
些对象共享相同的名称空间，则该函数将失败，并且Ge tLastError函数返回ERROR_INVALID_
HANDLE 。


返回值
如果函数成功，则返回值是新创建的互斥对象的句柄。
如果函数失败，则返回值为NULL。要荻得扩展的错误信息，请调用GetLastError。
如果互斥锁是一个已命名的互斥锁，并且该对象在此函数调用之前就存在，则返回值是现
有对象的句柄，GetLastError 返回ERROR_ALREADY_EXISTS。