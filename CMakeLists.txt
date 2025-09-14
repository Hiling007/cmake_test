cmake_minimum_required(VERSION 3.31)
project(cmake_test)
#项目名
#注意：以上两行代码必须紧挨着放在开头，不要在中间插入其他东西

#设置全局变量（c++标准、编译选项，。。。）
set(CMAKE_CXX_STANDARD 20)
set(OpenCV_DIR "/home/hiling/opencv_4_8_1/build/")

##配置阶段
#找到你需要依赖的包 ！注意，只是找到，链接需要在后面单独完成
	#会导出以下三个变量,可以在cmakelist后面调用：
#	<PackageName>_FOUND：是否找到该库。
#	<PackageName>_INCLUDE_DIRS：头文件路径。
#	<PackageName>_LIBRARIES：库文件路径。

#REQUIRE代表这个包是必须的，如果找不到则无法通过编译
#包名称后面跟版本号则是指定版本
#catkin在创建包的时候可以指定这几个
find_package(
#		catkin REQUIRED COMPONENTS roscpp rospy rm_bringup
		#COMPONENTS的作用：从包中加载组件（如果不是组件，则将该包加载为前面包的组件）
		#此处是从catkik这个包中加载roscpp、rospy两个组件,并把rm_bringup加载成catkin的组件。
		#好处是，会把后面的包统一整合到catkin的变量中，只需要调用caktin变量就可以调用后面的全部包。

		OpenCV REQUIRED
)

#generate_dynamic_reconfigure_options()
#catkin_package(
#		include
#		${catkin_INCLUDE_DIRS}
#		/home/hiling/...
#)


#catkin_package 作用：声明当前catkin包的信息（头文件在哪，到处库的名称，依赖哪些ros包/非ros包...）
#好处：可以正确传递依赖信息
	#其他包find_package该包后，会直接获得头文件路径，无需include_directories()
#注意与xml中标签名的协同
#如果库通过 target_link_libraries() 已链接依赖项，无需在 catkin_package() 中重复声明。
catkin_package(
		INCLUDE_DIRS include
		LIBRARIES ${PROJECT_NAME}

		#依赖的ros包
		CATKIN_DEPENDS
		roscpp
		std_msgs
		OpenCV

		#依赖的非ros包
		DEPENDS
		other_pkgs
)

#添加头文件路径
#以下是catkin下的头文件路径

#以下是一般情况下的头文件路径
include_directories(
		./h
		${OpenCV_INCLUDE_DIR}
)

#仅匹配src下的cpp文件
FILE(GLOB SOURCE_FILES "./*.cpp")
#递归匹配src目录下的cpp文件，会递归搜索子文件夹，谨慎使用
#CMake 官方文档警告：GLOB 不会自动检测新增文件，需手动重新运行 cmake
#FILE(GLOB_RECURSE SOURCE_FILES "src/*.cpp")

add_library(
#		${PROJECT_NAME}
		testtesttset
		main.cpp
)
#将包编译成一个库，可以通过名称调用这个库；
#此处为编译当先包，也可以指定别的目标，将其编译成另外的库

##构建阶段
#添加生成项
#第一个是生成的可运行文件，第二个是其对应的cpp
#${SOURCE_FILES}表示引用上面FILE搜索到的cpp文件
add_executable(cmake_test_exe ${SOURCE_FILES})

# 链接库（必须在 add_executable() 后调用）
# !!ros要在这里连接上catkin才能调用！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！
# 链接的目标是自己指定的库，或者findpackage找到的库文件路径。
target_link_libraries(cmake_test_exe ${OpenCV_LIBRARIES})
#注意套娃情况：
#target_link_libraries(my_app A B)  # B 依赖 A，则 B 在右


