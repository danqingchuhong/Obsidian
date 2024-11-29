### **LoadResource 函数详解**

`LoadResource` 是一个 Windows API 函数，用于将资源加载到内存中。它在成功调用 `FindResource` 获取资源句柄后使用，加载资源的二进制数据到内存，使其可以被程序访问。

---

### **函数定义**

```c
HGLOBAL LoadResource(
    HMODULE hModule, // 包含资源的模块句柄
    HRSRC hResInfo   // 资源句柄
);
```

---

### **参数详解**

1. **`hModule`**
    
    - 模块句柄，表示包含资源的模块（通常是 `.exe` 或 `.dll` 文件）。
    - 通常可以使用以下值：
        - `NULL`：表示当前模块（即调用者所在的可执行文件）。
        - 来自 `LoadLibrary` 或 `GetModuleHandle` 的模块句柄。
2. **`hResInfo`**
    
    - 由 `FindResource` 返回的资源句柄（`HRSRC`）。
    - 表示定位的资源信息。

---

### **返回值**

- 成功时：
    
    - 返回一个句柄（`HGLOBAL`），指向已加载的资源的内存地址。
    - 需要配合 `LockResource` 使用来访问具体的资源数据。
- 失败时：
    
    - 返回 `NULL`。
    - 调用 `GetLastError` 检查错误原因。

---

### **使用流程**

`LoadResource` 通常结合以下 API 函数使用：

1. **`FindResource`：** 定位资源。
2. **`LoadResource`：** 加载资源到内存。
3. **`LockResource`：** 获取资源的直接指针。
4. **（可选）SizeofResource：** 获取资源大小，用于正确处理数据。

---

### **示例代码**

#### 示例 1：加载和读取字符串资源

假设资源中有一个字符串表，其 ID 为 `101`：

```c
#include <windows.h>
#include <stdio.h>

int main() {
    HMODULE hModule = GetModuleHandle(NULL); // 当前模块句柄
    HRSRC hResInfo = FindResource(hModule, MAKEINTRESOURCE(101), RT_STRING);

    if (!hResInfo) {
        printf("Failed to find resource. Error: %d\n", GetLastError());
        return -1;
    }

    // 加载资源到内存
    HGLOBAL hResData = LoadResource(hModule, hResInfo);
    if (!hResData) {
        printf("Failed to load resource. Error: %d\n", GetLastError());
        return -1;
    }

    // 锁定资源获取指针
    LPCSTR pResourceData = (LPCSTR)LockResource(hResData);
    if (!pResourceData) {
        printf("Failed to lock resource. Error: %d\n", GetLastError());
        return -1;
    }

    // 资源数据的内容
    printf("Resource content: %s\n", pResourceData);

    return 0;
}
```

#### 示例 2：加载位图资源

加载位图资源并使用 `SizeofResource` 获取其大小：

```c
#include <windows.h>
#include <stdio.h>

int main() {
    HMODULE hModule = GetModuleHandle(NULL);
    HRSRC hResInfo = FindResource(hModule, MAKEINTRESOURCE(IDB_BITMAP1), RT_BITMAP);

    if (!hResInfo) {
        printf("Failed to find resource. Error: %d\n", GetLastError());
        return -1;
    }

    HGLOBAL hResData = LoadResource(hModule, hResInfo);
    if (!hResData) {
        printf("Failed to load resource. Error: %d\n", GetLastError());
        return -1;
    }

    DWORD resSize = SizeofResource(hModule, hResInfo);
    if (!resSize) {
        printf("Failed to get resource size. Error: %d\n", GetLastError());
        return -1;
    }

    LPVOID pResData = LockResource(hResData);
    if (!pResData) {
        printf("Failed to lock resource. Error: %d\n", GetLastError());
        return -1;
    }

    // 打印资源大小
    printf("Resource size: %lu bytes\n", resSize);

    return 0;
}
```

---

### **注意事项**

1. **资源锁定：**
    
    - `LoadResource` 只是加载资源数据，它返回的句柄需要通过 `LockResource` 才能获取直接内存指针。
2. **资源大小：**
    
    - 使用 `SizeofResource` 函数获取资源的实际大小。
    - 这是读取资源数据时非常重要的一步。
3. **资源释放：**
    
    - Windows 内部会自动管理 `LoadResource` 分配的资源，因此无需手动释放。
    - 旧版文档中提到 `FreeResource`，但现代系统中通常不需要使用。
4. **模块句柄（`hModule`）：**
    
    - 确保资源的模块句柄正确，尤其是资源位于动态链接库（DLL）时，需要通过 `LoadLibrary` 明确加载。

---

### **常见错误及调试**

1. **`LoadResource` 返回 `NULL`：**
    
    - 检查是否正确调用了 `FindResource` 并传递了有效的 `HRSRC`。
    - 确认模块句柄（`hModule`）有效。
2. **无法访问资源数据：**
    
    - 确保调用了 `LockResource` 并正确读取数据。
    - 检查资源的大小，避免越界访问。
3. **资源类型或标识符错误：**
    
    - 确保使用正确的 `RT_*` 类型或标识符，与资源定义保持一致。

---

### **总结**

`LoadResource` 是加载资源到内存的核心函数：

- 它是资源操作流程中的第二步，紧随 `FindResource`。
- 配合 `LockResource` 和 `SizeofResource`，可以高效地读取嵌入资源。
- 无需手动释放资源，但确保正确读取和解析资源数据。

这一功能广泛用于加载图标、位图、字符串表和自定义资源，是 Windows 程序资源管理的基础工具。