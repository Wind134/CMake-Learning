ADD_EXECUTABLE (main main.cpp)
LINK_DIRECTORIES (/usr/local/lib)       # 指定库目录
TARGET_LINK_LIBRARIES(main libhello.a)  # 再指令库目录下的具体库文件