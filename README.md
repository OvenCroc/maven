# 使用方法
1. project
在主项目的build.gradle文件的allprojects->repositories中添加<br>
`maven {
    url "https://raw.githubusercontent.com/OvenCroc/maven/master"
}`

2. module
在module的build.gradle文件dependencies中添加<br>
`implementation 'com.xhl.dyvideo.demand:dyvideo-relase:这里添加对于的版本号'`



# **dyvideo(万州视频组件库)**

1. XHLVideoView
    >描述:
    >这个view是继承GSYStandardPlayer重写的一个播放view，我们的项目最好也是用这个View进行开发，期间如果有什么问题可以直接联系我

    主要api:(因为直接是继承的，所以GSYStandardPlayer播放方法，同样适用于我们的XHLVideoView，这里我会主要介绍几种用法，之后会慢慢补充)
    * 初始化video信息 --> setVideoUrl(String videoUrl,boolean needCacheWhenPlay,String videoTitle)
    * 播放视频  -->  startPlay()
    * 暂停播放  --> onPause()
    * 继续播放  --> onResume()
    * 释放资源  --> onRelease()
    * 跳转播放  --> seekTo(long postion,boolean isNeedPlay)
    * 退出全屏  --> backToPortrait()  这个一般是配合activity的返回键用的，具体代码可以参考最下面的常见问题
    * 设置全屏播放的模式 --> setFullScreenInSameActivity(true) 点击全屏播放的时候判断是否是在当前页面播放，默认是在当前页面播放，效果也会好很多

    xml属性描述

    | 属性名        | 参数类型   |  功能描述  |
    | --------   | :-----:  | :----:  |
    | custom_layout| layout   |    自定义的控制器的layout，需要注意的是必须要保持必要view的id要跟我默认的default_video_layout里面的一样，最好是在默认布局的基础上改    |
    | custom_land_layout| layout |   自定义横屏情况下的控制器layout，注意事项同custom_layout   |
    | is_full_screen_in_same_activity|    boolean    |  点击全屏播放的时候判断是否是在当前页面播放，默认是在当前页面播放，效果也会好很多  |

    使用方法:

    * 在需要使用播放器的xml中添加XHLVideoView:

        ```html
        <com.xhl.videocomponet.customview.XHLVideoView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:custom_layout="@layout/custom_video_controller_layout"
            android:id="@+id/player"
            />
        ```
        需要注意的是这里的app:custom_layout参数是可以不传的,如果不传默认的就是使用我视频组件项目里面的控制器布局default_video_layout.xml，否则就是使用的自定义布局，如果有自定义layout的需求，那自定义的layout必须满足一下几点要求
        自定义布局必须包含的基本view是
       1. id="surface_container"，这个view可以是ViewGroup类型的容器类，主要作用是用来添加gsytexture类，如果没有这个view就无法播放
       2. 播放按钮的id是start
       3. 进度条id是progress

        >具体其他ui的id可以通过项目中的default_video_layout来查看, 如果感兴趣也可以直接看看gsy的源码。  
        (更新中...)

    * 在页面中找到对应的view通过上面的api对视频的播放进行操作即可
        ```java
        public class NormalPlayerAcitivity extends BaseActivity {
            @Override
            protected void onCreate(@Nullable Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.normal_player_activity_layout);
                String videoUrl = "http://9890.vod.myqcloud.com/9890_4e292f9a3dd011e6b4078980237cc3d3.f20.mp4";
                boolean cacheWhenPlay = true;
                String title = "我是title";
                XHLVideoView player = (XHLVideoView) findViewById(R.id.player);
                player.setUp(videoUrl, cacheWhenPlay, title);
            }
        }
        ```

     遇到的问题:
   1. 如果出现点击全屏崩溃的情况首先确认该Actvity在Manifest中是否有添加如下代码
        ```html
        android:configChanges="keyboard|keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize|uiMode"
        ```
      然后再看看Activity是否是集成的AppcomActivity，或者FragmentActivity，目前只支持这两种activity
   2. 如果出现在全屏时候，点击返回，页面直接被关闭而不是恢复到竖屏状态，请检查该Activity代码中是否有添加如下代码

        ```java
        @Override
        public void onBackPressed() {
            if (player.backToPortrait()) {
                return;
            } else {
                super.onBackPressed();
            }
        } 
         ```
---
2. 仿抖音页面

   使用方法:

   * 在自己的项目中,新建一个activity继承`VideoDouYinListActivity`,实现对应的5个方法如下:

      * onThumbsUpClick  点赞事件
      * doCommentClick  点击评论事件
      * doShareClick 点击分享事件
      * backClick 点击左上角返回事件
      * topShareClick 点击右上角按钮事件

      > 自己在对应的方法中实现对应的功能即可

   * 仿抖音界面所涉及到的类以及介绍

     1.  VideoDouYinListActivity 主要的仿抖音界面
     2.  DouyinAdapterOption 抖音界面配置类, 主要配置滑动事件的拦截，包括是否支持上下滑动，是否支持左右滑动，进度条是否支持滑动，以及新闻描述文字的行数等，具体api如下

            | 方法名        | 参数类型   |  功能描述  |
            | --------   | :-----:  | :----:  |
            | setDesMaxLine| int   |    设置新闻行数最大值   |
            | setSeekbarScroll| boolean |   是否允许滑动进度条   |
            | setVerticalScroll|    boolean    |  设置是否可以垂直滑动  |
            | setHorizonScroll |    boolean    |  设置是否可以水平滑动,主要是设置播放器的touch事件，里面就包括横屏快进快退，上滑下滑调节亮度和音量的功能  |
            > 使用方法就是在刚刚新建的集成于 `VideoDouYinListActivity` 的activity中的oncreate方法(或者其他的位置) 调用如下代码
            >   ```java
            >    @Override
            >    protected void onCreate(@Nullable Bundle savedInstanceState) {
            >        super.onCreate(savedInstanceState);
            >        adapter.setOption(
            >                new DouyinAdapterOption()
            >                        .setDesMaxLine(4)//设置新闻行数最大值
            >                        .setHorizonScroll(false)//是否能滑动快进,调声量
            >                        .setVerticalScroll(false)//是否允许竖直方向滑动
            >                        .setSeekbarScroll(true)//是否允许seekbar滑动
            >        );
            >    }

