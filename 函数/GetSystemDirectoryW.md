`GetSystemDirectoryW` 是 Windows API 函数，用于检索操作系统目录（通常是 `System32` 文件夹）的路径。这个目录包含了 Windows 系统的核心文件、DLL 和工具。

---

## **函数原型**

```c
UINT GetSystemDirectoryW(
    LPWSTR lpBuffer,
    UINT   uSize
);
```

### 参数说明

1. **`lpBuffer`**  
   - 类型：`LPWSTR`  
   - 含义：接收系统目录路径的缓冲区指针。  
   - 注意事项：缓冲区必须足够大以容纳路径和终止的 `null` 字符。

2. **`uSize`**  
   - 类型：`UINT`  
   - 含义：`lpBuffer` 缓冲区的大小（以字符为单位）。  

### 返回值

- **成功**：返回写入到缓冲区的字符数（包括终止的 `null` 字符）。  
- **失败**：返回 `0`，可以调用 `GetLastError` 检索错误信息。  

---

## **功能与行为**

### 返回的路径
- **默认返回路径**：  
  `C:\Windows\System32`（假设 Windows 安装在 `C:\Windows` 中）。  

- **路径格式**：  
  返回的路径是完整的绝对路径，并带有尾部的 `null` 终止符。例如：  
  ```plaintext
  C:\Windows\System32
  ```

---

## **使用方法**

### 示例代码

以下示例代码展示如何调用 `GetSystemDirectoryW` 并打印返回的系统目录路径。

```c
#include <windows.h>
#include <stdio.h>

int main() {
    WCHAR systemDir[MAX_PATH];  // 定义缓冲区
    UINT size = GetSystemDirectoryW(systemDir, MAX_PATH);  // 获取路径

    if (size > 0 && size < MAX_PATH) {
        wprintf(L"System directory: %ls\n", systemDir);  // 打印路径
    } else {
        wprintf(L"Failed to get system directory. Error: %lu\n", GetLastError());
    }
    return 0;
}
```


## **相关函数对比**

| 函数                   | 功能                                          | 返回示例                     |
|------------------------|---------------------------------------------|-----------------------------|
| `GetSystemDirectoryW`  | 返回系统目录（如 `System32`）                | `C:\Windows\System32`       |
| `GetWindowsDirectoryW` | 返回 Windows 根目录                         | `C:\Windows`                |
| `GetSystemWow64DirectoryW` | 返回 WOW64 系统目录（32 位进程特定）      | `C:\Windows\SysWOW64`       |
| `SHGetFolderPath`      | 获取特殊文件夹路径（如程序文件夹、桌面等）   | `C:\Program Files`          |

---

