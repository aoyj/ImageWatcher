#ImageWatcher
图片查看器，为各位追求用户体验的daLao提供更优质的服务
它能够

*点击图片时以一种无缝顺畅的动画切换到图片查看的界面，同样以一种无缝顺畅的动画退出图片查看界面
*支持多图查看，快速翻页，双击放大，单击退出，双手缩放旋转图片
*下拽退出查看图片的操作，以及效果是本View的最大卖点(仿微信)

![image](https://github.com/byc4426/ImageWatcher/tree/master/previews/111.gif)
![image](https://github.com/byc4426/ImageWatcher/tree/master/previews/222.gif)
![image](https://github.com/byc4426/ImageWatcher/tree/master/previews/333.gif)
![image](https://github.com/byc4426/ImageWatcher/tree/master/previews/444.gif)



## 实现步骤

在module的gradle
```
    implementation 'byc.imagewatcher:imagewatcher:1.0.0'
```

#### 方法一
* 在Activity对应布局文件
```
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- some layout here -->

    <byc.imagewatcher.ImageWatcher
        android:id="@+id/v_image_watcher"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</FrameLayout>
```

* 然后在Activity onCreate里面简单的初始化一下

```
        vImageWatcher = (ImageWatcher) findViewById(R.id.v_image_watcher); // 一般来讲， ImageWatcher 需要占据全屏的位置
        vImageWatcher.setTranslucentStatus(!isTranslucentStatus ? Utils.calcStatusBarHeight(this) : 0);  // 如果是透明状态栏，你需要给ImageWatcher标记 一个偏移值，以修正点击ImageView查看的启动动画的Y轴起点的不正确
        vImageWatcher.setErrorImageRes(R.mipmap.error_picture);  // 配置error图标 如果不介意使用lib自带的图标，并不一定要调用这个API
        vImageWatcher.setOnPictureLongPressListener(this); // 长按图片的回调，你可以显示一个框继续提供一些复制，发送等功能
        vImageWatcher.setLoader(new ImageWatcher.Loader() {//调用show方法前，请先调用setLoader 给ImageWatcher提供加载图片的实现
            @Override
            public void load(Context context, String url, final ImageWatcher.LoadCallback lc) {
                Glide.with(context).asBitmap().load(url).into(new SimpleTarget<Bitmap>() {
                    @Override
                    public void onResourceReady(Bitmap resource, Transition<? super Bitmap> transition) {
                        lc.onResourceReady(resource);
                    }

                    @Override
                    public void onLoadStarted(Drawable placeholder) {
                        lc.onLoadStarted(placeholder);
                    }

                    @Override
                    public void onLoadFailed(Drawable errorDrawable) {
                        lc.onLoadFailed(errorDrawable);
                    }
                });
            }
        });
```

#### 新的初始化方式二
```
        vImageWatcher = ImageWatcher.Helper.with(this) // 一般来讲， ImageWatcher 需要占据全屏的位置
                .setTranslucentStatus(!isTranslucentStatus ? Utils.calcStatusBarHeight(this) : 0) // 如果是透明状态栏，你需要给ImageWatcher标记 一个偏移值，以修正点击ImageView查看的启动动画的Y轴起点的不正确
                .setErrorImageRes(R.mipmap.error_picture) // 配置error图标 如果不介意使用lib自带的图标，并不一定要调用这个API
                .setOnPictureLongPressListener(this) // 长按图片的回调，你可以显示一个框继续提供一些复制，发送等功能
                .setLoader(new ImageWatcher.Loader() {//调用show方法前，请先调用setLoader 给ImageWatcher提供加载图片的实现
                    @Override
                    public void load(Context context, String url, final ImageWatcher.LoadCallback lc) {
                        Glide.with(context).asBitmap().load(url).into(new SimpleTarget<Bitmap>() {
                            @Override
                            public void onResourceReady(Bitmap resource, Transition<? super Bitmap> transition) {
                                lc.onResourceReady(resource);
                            }

                            @Override
                            public void onLoadStarted(Drawable placeholder) {
                                lc.onLoadStarted(placeholder);
                            }

                            @Override
                            public void onLoadFailed(Drawable errorDrawable) {
                                lc.onLoadFailed(errorDrawable);
                            }
                        });
                    }
                })
                .create();
```

由于一般图片查看会占据全屏
持有activity引用后 调用`activity.getWindow().getDecorView()`拿到根FrameLayout
即可动态插入ImageWatcher -> 使用 `ImageWatcher.Helper.with(activity)` 插入ImageWatcher
非入侵式 不再需要在布局文件中加入&lt;ImageWatcher&gt;标签 减少布局嵌套


这个时候你的所有准备工作已经完成
```
/**
 * @param i              被点击的ImageView
 * @param imageGroupList 被点击的ImageView的所在列表，加载图片时会提前展示列表中已经下载完成的thumb图片
 * @param urlList        被加载的图片url列表，数量必须大于等于 imageGroupList.size。 且顺序应当和imageGroupList保持一致
 */
public void show(ImageView i, List<ImageView> imageGroupList, final List<String> urlList) { ... }
```

最后只要调用 `vImageWatcher.show()` 方法就可以了

可以具体看源码demo，这个项目是可以运行的，这个项目是可以运行的，这个项目是可以运行的


## 写在最后
为什么要写这个Demo？

*能够给在项目上有这个功能需求而又愿意试水此库的各位daLao节约一些开发时间
*为了更好的视觉体验
*希望能够给在项目上有这个功能需求而又愿意试水的各位daLao节约一些开发时间
更推荐自己copy代码定制，有不好的地方，和更好的想法欢迎提出来

如果这些代码对你提供了帮助，你的Star是对本宝最大的支持。
