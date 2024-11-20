```
BOOL WINAPI DllMain(
    HINSTANCE hinstDLL,  // DLL 的句柄
    DWORD fdwReason,     // 调用原因
    LPVOID lpvReserved   // 预留参数
);
```
#### 参数说明
1. **`hinstDLL`**
    
    - 类型：`HINSTANCE`
    - 含义：当前 DLL 模块的实例句柄。它可以用来获取 DLL 文件的资源或路径。
2. **`fdwReason`**
    
    - 类型：`DWORD`
    - 含义：指示为什么 `DllMain` 被调用。主要取以下值：
        - `DLL_PROCESS_ATTACH`：DLL 被加载到进程时触发。
        - `DLL_THREAD_ATTACH`：进程中的新线程创建时触发。
        - `DLL_THREAD_DETACH`：线程退出时触发。
        - `DLL_PROCESS_DETACH`：DLL 从进程中卸载时触发。
3. **`lpvReserved`**
    
    - 类型：`LPVOID`
    - 含义：
        - 如果 `fdwReason` 是 `DLL_PROCESS_ATTACH` 或 `DLL_PROCESS_DETACH`，此参数可以是：
            - `NULL`：显式加载或卸载（通过 `LoadLibrary` 或 `FreeLibrary`）。
            - 非 `NULL`：隐式加载或卸载（进程启动或终止时）。
        - 对于 `DLL_THREAD_ATTACH` 和 `DLL_THREAD_DETACH`，这个参数始终为 `NULL`。

### 返回值

- 类型：`BOOL`
- 含义：返回 `TRUE` 表示成功，返回 `FALSE` 则加载或卸载失败。
#### `fdwReason` 的取值含义
`fdwReason` 是一个枚举类型，可能的值如下：

1. **`DLL_PROCESS_ATTACH` (0x00000001)**
    
    - **含义**：当 DLL 被加载到某个进程的地址空间时触发。
    - **场景**：
        - 当进程通过 `LoadLibrary` 或隐式加载 DLL 时。
    - **注意**：在此阶段，不能调用依赖其他 DLL 的函数，因为加载可能尚未完成。
    - **开发者动作**：通常在此设置初始化代码或分配资源。
2. **`DLL_THREAD_ATTACH` (0x00000002)**
    
    - **含义**：当进程中的一个新线程创建时触发。
    - **场景**：
        - 当线程在加载 DLL 的进程中启动时。
    - **注意**：如果进程已经加载了 DLL，则此事件在每个新线程启动时都会调用。
    - **开发者动作**：很少需要在此处理，通常可以忽略。
3. **`DLL_THREAD_DETACH` (0x00000003)**
    
    - **含义**：当进程中的线程终止时触发。
    - **场景**：
        - 某线程退出时，DLL 会收到此通知。
    - **注意**：可以在此释放线程特定的资源。
    - **开发者动作**：很少需要处理，只有在使用线程局部存储（TLS）时才会用到。
4. **`DLL_PROCESS_DETACH` (0x00000000)**
    
    - **含义**：当 DLL 从进程地址空间卸载时触发。
    - **场景**：
        - 当进程通过 `FreeLibrary` 卸载 DLL，或进程终止时。
    - **注意**：应在此释放分配的资源。
    - **开发者动作**：通常在此处理清理工作。