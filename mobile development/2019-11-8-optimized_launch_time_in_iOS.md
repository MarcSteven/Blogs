# improve launch Times
## pre_main()
1，使用DYLD_PRINT_STATISTICS 环境变量
* 在shipping Oses上可用的
* 在seed2中可用的
2，使用debugger终止每个dylib加载

 使用更少的dylibs-合并存在的动态库，使用静态归档
 不要去动态库里面再去依赖动态库
 懒加载。
 ** dlopen() 会引起问题 **
 # Rebase/binding
 * 减少_DATA 指针
 * 减少OC的元数据-类 selector和分类
 * 减少C++ 虚拟化
 * 使用Swift的结构体
 * 检查机器生成的代码-使用offsets代替指针，标识为可读
 Objc安装
 * 类注册
 * 非脆弱的ivars抵消更新
 * 分类注册
 * selector统一
 初始化器
 使用+initiailize 替代ObjC+ load方法
 使用调用site初始化替代-dispatch_once(),pthread_once(),std::once()
 C/C++__attribute_((constructor))
 使用swift重写
 不雅在初始化器中调动dlopen()
 在初始化器中不要创建线程
 
 
 
 


