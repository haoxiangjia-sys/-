---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[exception]]"
  - "[[multithreading]]"
---

# File 与 IO 流

## File 类

**`java.io.File` — 代表文件或文件夹的路径，可以操作文件/目录的增删查**

> 注意：File 类操作的是**文件路径本身**，不是文件内容（读写内容用 IO 流）

### 创建 File 对象

```java
// 方式一：直接传路径
File f1 = new File("E:\\test\\a.txt");

// 方式二：父路径 + 子路径
File f2 = new File("E:\\test", "a.txt");

// 方式三：父 File 对象 + 子路径
File parent = new File("E:\\test");
File f3 = new File(parent, "a.txt");
```

### 常用方法

#### 获取信息

| 方法 | 说明 |
| ---- | ---- |
| `getName()` | 获取文件名或文件夹名 |
| `getPath()` | 获取构造时传入的路径 |
| `getAbsolutePath()` | 获取绝对路径 |
| `length()` | 获取文件大小（字节） |
| `lastModified()` | 获取最后修改时间（时间戳） |

#### 判断功能

| 方法 | 说明 |
| ---- | ---- |
| `exists()` | 判断文件或文件夹是否存在 |
| `isFile()` | 判断是否为文件 |
| `isDirectory()` | 判断是否为文件夹 |
| `isAbsolute()` | 判断是否为绝对路径 |

#### 增删功能

| 方法 | 说明 |
| ---- | ---- |
| `createNewFile()` | 创建新文件（不存在时才创建） |
| `mkdir()` | 创建单级文件夹 |
| `mkdirs()` | 创建多级文件夹（**推荐**） |
| `delete()` | 删除文件或空文件夹 |
| `renameTo(File dest)` | 重命名或移动 |

#### 目录遍历

| 方法 | 说明 |
| ---- | ---- |
| `list()` | 获取目录下的所有文件名（String[]） |
| `listFiles()` | 获取目录下的所有文件对象（File[]） |

### 示例

```java
File dir = new File("E:\\test");
File file = new File(dir, "a.txt");

// 创建文件夹
if (!dir.exists()) {
    dir.mkdirs();          // 创建多级目录
}

// 创建文件
if (!file.exists()) {
    file.createNewFile();
}

// 获取信息
System.out.println(file.getName());        // a.txt
System.out.println(file.length());         // 0（刚创建）
System.out.println(file.isFile());         // true

// 遍历目录
File[] files = dir.listFiles();
for (File f : files) {
    System.out.println(f.getName());
}
```

> **路径分隔符**：Windows 用 `\`，Linux/Mac 用 `/`  
> 跨平台推荐用 `File.separator` 常量

---

## IO 流

**IO 流 = 用来读写文件数据（输入/输出）**

### IO 流分类

```
字节流（读写所有类型文件）
├── InputStream（输入：读文件）
│   ├── FileInputStream        文件输入流
│   └── BufferedInputStream    缓冲输入流
└── OutputStream（输出：写文件）
    ├── FileOutputStream       文件输出流
    └── BufferedOutputStream   缓冲输出流

字符流（读写文本文件）
├── Reader（输入：读文本）
│   ├── FileReader             文件字符输入流
│   ├── BufferedReader         缓冲字符输入流
│   └── InputStreamReader      转换流（字节→字符）
└── Writer（输出：写文本）
    ├── FileWriter              文件字符输出流
    ├── BufferedWriter          缓冲字符输出流
    └── OutputStreamWriter      转换流（字符→字节）
```

| | 字节流 | 字符流 |
| -- | ----- | ------ |
| **读写单位** | 字节（byte） | 字符（char） |
| **适用** | 图片、视频、音频、任意文件 | 纯文本文件（.txt .java） |
| **基类** | InputStream / OutputStream | Reader / Writer |

### 字节流（读写所有文件）

```java
// 写文件
FileOutputStream fos = new FileOutputStream("E:\\test.txt");
fos.write("Hello".getBytes());     // 写入字节
fos.close();                       // 关闭流

// 读文件
FileInputStream fis = new FileInputStream("E:\\test.txt");
int b;
while ((b = fis.read()) != -1) {   // 一个个字节读
    System.out.print((char) b);
}
fis.close();
```

### 字符流（读写文本文件）

```java
// 写文件
FileWriter fw = new FileWriter("E:\\test.txt");
fw.write("Hello World");
fw.close();

// 读文件
FileReader fr = new FileReader("E:\\test.txt");
int ch;
while ((ch = fr.read()) != -1) {
    System.out.print((char) ch);
}
fr.close();
```

### 缓冲流（推荐，效率更高）

```java
// 缓冲输出
BufferedWriter bw = new BufferedWriter(new FileWriter("E:\\test.txt"));
bw.write("Hello");
bw.newLine();                      // 换行
bw.write("World");
bw.close();                        // 关外层即可，内层自动关

// 缓冲输入
BufferedReader br = new BufferedReader(new FileReader("E:\\test.txt"));
String line;
while ((line = br.readLine()) != null) {   // 一次读一行
    System.out.println(line);
}
br.close();
```

### try-with-resources 自动关流

```java
// 不用手动 close，自动关
try (FileWriter fw = new FileWriter("E:\\test.txt");
     BufferedWriter bw = new BufferedWriter(fw)) {

    bw.write("自动关流");
} catch (IOException e) {
    e.printStackTrace();
}
// 不用写 finally 关流了！
```

### IO 流使用场景速查

| 场景 | 用什么 |
| ---- | ------ |
| 读文本文件（.txt .java） | `BufferedReader` + `FileReader` |
| 写文本文件 | `BufferedWriter` + `FileWriter` |
| 读二进制文件（图片、视频） | `BufferedInputStream` + `FileInputStream` |
| 写二进制文件 | `BufferedOutputStream` + `FileOutputStream` |
| 按行读 | `BufferedReader.readLine()` |
| 指定编码读 | `BufferedReader` + `InputStreamReader` + `FileInputStream` |

---

### 附录：IO 流底层原理

#### 字节流缓冲区：一次读一个字节数组

```java
// ❌ 慢：一次读一个字节
FileInputStream fis = new FileInputStream("big.mp4");
int b;
while ((b = fis.read()) != -1) {   // 读一次，硬盘IO一次
    // ...
}

// ✅ 快：一次读一个字节数组（像卡车拉货）
FileInputStream fis = new FileInputStream("big.mp4");
byte[] buffer = new byte[8192];     // 8KB 缓冲区
int len;
while ((len = fis.read(buffer)) != -1) {  // 一次读一堆
    // buffer 里装了 len 个字节
}
```

> 原理：`read()` 一次读 1 字节 = 自行车拉货；`read(byte[])` 一次读 8KB = 卡车拉货

#### 文件拷贝实战

```java
public static void copyFile(String src, String dest) throws IOException {
    try (FileInputStream fis = new FileInputStream(src);
         FileOutputStream fos = new FileOutputStream(dest)) {

        byte[] buffer = new byte[8192];
        int len;
        while ((len = fis.read(buffer)) != -1) {
            fos.write(buffer, 0, len);   // 读多少写多少
        }
    }
}
```

#### FileOutputStream 追加写

```java
// 默认：覆盖写
FileOutputStream fos1 = new FileOutputStream("test.txt");
fos1.write("覆盖".getBytes());

// true = 追加写（续写，不覆盖原有内容）
FileOutputStream fos2 = new FileOutputStream("test.txt", true);
fos2.write("追加".getBytes());
```

#### 字符集与编码

**乱码的原因**：编码和解码用的字符集不一样

```java
// GBK 编码 → UTF-8 解码 = 乱码
byte[] bytes = "你好".getBytes("GBK");       // 编码
String str = new String(bytes, "UTF-8");    // 解码 → 乱码！
```

| 字符集 | 说明 | 中文占几个字节 |
| ------ | ---- | ------------- |
| **ASCII** | 英文编码，1字节 | 不支持中文 |
| **GBK** | 中文老标准 | 2 字节 |
| **UTF-8** | 国际通用标准（推荐） | 3 字节 |

```java
// Java 中编码解码
String str = "你好";

byte[] utf8 = str.getBytes("UTF-8");        // 编码：文字 → 字节
String s1 = new String(utf8, "UTF-8");      // 解码：字节 → 文字（不乱码）
String s2 = new String(utf8, "GBK");        // 解码用错字符集 → 乱码！
```

#### 字符流底层原理

**字符流 = 字节流 + 编码表 + 内存缓冲区**

```java
// 字符流内部做了什么？
FileReader fr = new FileReader("test.txt");
// 1. 底层用的是 FileInputStream（字节流）
// 2. 自动按 UTF-8 把字节解码成字符
// 3. 内部有一个 8KB 的缓冲区，减少硬盘IO

int ch;
while ((ch = fr.read()) != -1) {  // 读的是字符，不是字节
    System.out.print((char) ch);
}
```

#### flush() 的作用

**把缓冲区里的数据强制刷到硬盘**

```java
// 没有 flush()：数据可能还在缓冲区，没写到文件
BufferedWriter bw = new BufferedWriter(new FileWriter("test.txt"));
bw.write("Hello");
// 没 close、没 flush → 如果程序崩了，Hello 可能没写进去

// flush()：立马刷到硬盘
bw.flush();      // 还能继续写
bw.write(" World");
bw.close();      // close() 内部会自动 flush()
```

> **flush vs close**：`flush()` 刷完还能继续写；`close()` 刷完就关闭流，不能再写了

---

**相关笔记**: [[../Java-basics/java-basics]] · [[exception]] · [[multithreading]]
