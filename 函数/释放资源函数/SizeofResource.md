### **SizeofResource 函数详解**

`SizeofResource` 是一个 Windows API 函数，用于获取指定资源的大小（以字节为单位）。在访问资源数据之前，确定其大小对于避免越界访问或错误读取资源非常重要。

---

### **函数定义**

```c
DWORD SizeofResource(
    HMODULE hModule, // 包含资源的模块句柄
    HRSRC hResInfo   // 资源句柄
);
```

---

### **参数详解**

1. **`hModule`**
    
    - 指定资源所在的模块的句柄。
    - 通常可以是：
        - `NULL`：表示当前模块（即调用者所在的可执行文件）。
        - 通过 `LoadLibrary` 或 `GetModuleHandle` 获取的模块句柄。
2. **`hResInfo`**
    
    - 由 `FindResource` 返回的资源句柄（`HRSRC`）。
    - 表示目标资源的标识信息。

---

### **返回值**

- 成功时：
    - 返回资源的大小（以字节为单位），类型为 `DWORD`。
- 失败时：
    - 返回值为 `0`。
    - 可以通过 `GetLastError` 检查错误原因。

---

### **功能说明**

- `SizeofResource` 是资源管理流程中的辅助函数，用于获取资源的精确大小。
- 它与 `FindResource` 和 `LoadResource` 配合使用，确保资源访问时不会读取超出数据范围的内容。

---

### **使用流程**

以下是完整的资源加载和读取流程：

1. **定位资源**：调用 `FindResource` 获取资源句柄。
2. **获取资源大小**：调用 `SizeofResource` 获取资源的字节大小。
3. **加载资源**：调用 `LoadResource` 将资源加载到内存。
4. **锁定资源**：调用 `LockResource` 获取资源内存指针。
5. **访问资源数据**：使用指针读取资源内容，根据 `SizeofResource` 确保安全读取。

---

### **示例代码**

#### 示例 1：获取字符串资源大小

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

    // 获取资源大小
    DWORD resSize = SizeofResource(hModule, hResInfo);
    if (!resSize) {
        printf("Failed to get resource size. Error: %d\n", GetLastError());
        return -1;
    }

    printf("Resource size: %lu bytes\n", resSize);

    return 0;
}
```

#### 示例 2：加载和保存二进制资源

假设资源中包含一个二进制文件：

```c
#include <windows.h>
#include <stdio.h>

int main() {
    HMODULE hModule = GetModuleHandle(NULL);
    HRSRC hResInfo = FindResource(hModule, MAKEINTRESOURCE(IDR_BINARY1), "CUSTOM");

    if (!hResInfo) {
        printf("Failed to find resource. Error: %d\n", GetLastError());
        return -1;
    }

    DWORD resSize = SizeofResource(hModule, hResInfo);
    if (!resSize) {
        printf("Failed to get resource size. Error: %d\n", GetLastError());
        return -1;
    }

    HGLOBAL hResData = LoadResource(hModule, hResInfo);
    if (!hResData) {
        printf("Failed to load resource. Error: %d\n", GetLastError());
        return -1;
    }

    LPCVOID pResData = LockResource(hResData);
    if (!pResData) {
        printf("Failed to lock resource. Error: %d\n", GetLastError());
        return -1;
    }

    // 保存资源到文件
    FILE *file = fopen("output.bin", "wb");
    if (file) {
        fwrite(pResData, 1, resSize, file);
        fclose(file);
        printf("Resource written to output.bin\n");
    } else {
        printf("Failed to open file for writing.\n");
    }

    return 0;
}
```

---

### **注意事项**

1. **资源大小为 0：**
    
    - 如果 `SizeofResource` 返回 0，可能是因为：
        - `hModule` 无效，检查是否正确加载模块。
        - `hResInfo` 无效，确认是否成功调用 `FindResource`。
    - 调用 `GetLastError` 获取具体错误代码。
2. **模块有效性：**
    
    - 如果资源在外部 DLL 中，确保正确加载 DLL 并获取其模块句柄。
3. **与 `LoadResource` 配合：**
    
    - `SizeofResource` 不直接加载资源，而是配合 `LoadResource` 和 `LockResource` 使用，获取资源的大小和数据。
4. **类型匹配：**
    
    - 确保 `FindResource` 的 `lpType` 和 `lpName` 与资源定义一致，否则会导致资源无法找到。

---

### **常见错误及调试**

1. **返回值为 `0`：**
    
    - 检查是否正确调用了 `FindResource`。
    - 确保传入的模块句柄有效。
2. **资源大小不正确：**
    
    - 资源可能未正确嵌入到可执行文件或 DLL 中，检查资源脚本（如 `.rc` 文件）是否正确配置。
3. **资源内容读取失败：**
    
    - 检查是否正确加载资源并锁定内存。
    - 使用 `SizeofResource` 确保读取的数据大小正确。

---

### **总结**

- **`SizeofResource`** 是资源管理流程中的关键函数，用于获取资源的实际大小。
- 它返回的大小是字节数，可以直接用于内存操作或文件写入。
- 配合 `FindResource`、`LoadResource` 和 `LockResource`，可以高效、安全地管理 Windows 程序中的嵌入资源。

这是资源访问的基础工具，确保正确读取和解析各种资源（如图标、位图、字符串表、自定义数据等）。