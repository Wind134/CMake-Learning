# 工程目录下的CMake
PROJECT (HELLO)              # 工程名为HELLO
ADD_SUBDIRECTORY (src bin)   # 源码目录src以及二进制保存的目录bin，这里写了src目录，所以会进入到src目录去寻找源码
# 以下是cmake的安装指令
INSTALL (FILES README COPYRIGHT DESTINATION  share/doc/cmake\ learning)
INSTALL (PROGRAMS run.sh DESTINATION share/doc/cmake\ learning/bin/)
INSTALL (DIRECTORY doc/ DESTINATION share/doc/cmake)
INSTALL (PROGRAMS run.sh DESTINATION bin)

# SET (SRC_LIST hello.cpp)

# MESSAGE (STATUS "This is BINARY dir" ${PROJECT_BINARY_DIR})
# MESSAGE (STATUS "This is SOURCE dir" ${PROJECT_SOURCE_DIR})

# ADD_EXECUTABLE(hello ${SRC_LIST})	
