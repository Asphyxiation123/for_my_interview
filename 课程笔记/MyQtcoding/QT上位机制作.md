# 1. Qcustomplot 使用
该绘图库需要到其官网上下载，下载后关键文件为：
```shell
qcustomplot.cpp
qcustomplot.h
```

## cmake 调用
在 cmakez 中需要加入以下语句
```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-mbig-obj")

#加入PrintSupport的支持
find_package(Qt6 REQUIRED COMPONENTS PrintSupport)

#库链接
target_link_libraries(plot_test1 PRIVATE Qt6::PrintSupport)
```
以下语句可以指定待链接的库地址
```cmake
INCLUDE_DIRECTORIES(
    ${PROJECT_SOURCE_DIR}/qcustomplot
)
```