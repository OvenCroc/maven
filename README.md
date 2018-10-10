# maven
我的仓库

# 使用方法
1. project
在主项目的build.gradle文件的allprojects->repositories中添加

`maven {
    url "https://raw.githubusercontent.com/OvenCroc/maven/master"
}`

2. module
在module的build.gradle文件dependencies中添加

`implementation 'com.feinno.androidframe:androidframe-release:这里添加对于的版本号'`

ok了...

