# 使用方法
1. project
在主项目的build.gradle文件的allprojects->repositories中添加<br>
`maven {
    url "https://raw.githubusercontent.com/OvenCroc/maven/master"
}`

2. module
在module的build.gradle文件dependencies中添加<br>
`implementation 'com.feinno.androidframe:androidframe-release:这里添加对于的版本号'`



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
        需要注意的是这里的app:custom_layout参数是可以不传的,如果不传默认的就是使用我视频组件项目里面的控制器布局video_layout_standard.xml，否则就是使用的自定义布局，如果有自定义layout的需求，那自定义的layout必须满足一下几点要求
        自定义布局必须包含的基本view有(更新中...)
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

        ```java @Override
        public void onBackPressed() {
            if (player.backToPortrait()) {
                return;
            } else {
                super.onBackPressed();
            }
        } 
         ```