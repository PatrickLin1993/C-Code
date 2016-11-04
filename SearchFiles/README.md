##C++遍历文件夹

1. 库 ： `<io.h>`
2. 数据结构 `_finddata_t`
	
```cpp
struct _finddata_t
{	
	unsigned attrib;         //文件属性
    time_t time_create;      //文件创建时间
    time_t time_access;      //文件上一次访问时间
    time_t time_write;       //文件上一次修改时间
	_fsize_t size;           //文件字节数
    char name[_MAX_FNAME];   //文件名
}
```
其中文件属性 `attrib` 是无符号整型，取值为为相应的宏：`_A_SUBDIR` 文件夹，`_A_HIDDEN` 隐藏，`_A_SYSTEM` 系统，`_A_NORMAL` 正常，`_A_RDONLY` 只读。

```
`_findfirst` 根据命名规则匹配当前目录的第一个文件，返回的是匹配的文件的句柄，数据类型为`long`。
`_findnext` 根据命名规则匹配到当前目录的下一个文件。
`_findclose` 关闭句柄。
```

下面是搜索当前目录的所有文件。
```cpp
// Search All File In Current Direction
void SearchFileCurtDir(string path, vector<string>& result)
{
	long handle = 0;
	_finddata_t fileInfo;
	string pathName;

	if ((handle = _findfirst(pathName.assign(path).append("\\*").c_str(), &fileInfo)) == -1){
		cerr << "failed to find any files !" << endl;
		return;
	}

	do{
		result.push_back(fileInfo.name);
	} while (_findnext(handle, &fileInfo) == 0);

	_findclose(handle);
}
```

下面是搜索当前目录下，包括所有子目录下的所有文件。
```cpp
// Search All File In Complete Direction
void SearchFileCompDir(string path, vector<string>& result)
{
	long handle = 0;
	_finddata_t fileInfo;
	string pathName;

	if ((handle = _findfirst(pathName.assign(path).append("\\*").c_str(), &fileInfo)) == -1){
		cerr << "failed to find any files !" << endl;
		return;
	}

	do{
		// 是否含有子目录
		if (fileInfo.attrib & _A_SUBDIR){
			if (strcmp(fileInfo.name, ".") != 0 && strcmp(fileInfo.name, "..") != 0){
				SearchFileCompDir(path + "\\" + fileInfo.name, result);
			}
		}
		else{
			result.push_back(fileInfo.name);
		}
	} while (_findnext(handle, &fileInfo) == 0);

	_findclose(handle);
}
```