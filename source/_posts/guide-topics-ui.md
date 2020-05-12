---
title: Android 用户页面和导航
top: false
cover: false
toc: true
mathjax: true
date: 2020-05-12 11:31:10
password:
summary:
tags: [android, guide, ui, 翻译, 进行中]
categories:
---

## 布局

## 通知概览

## 添加 app bar

## 控制系统 UI 显示性

### 概览

### 调暗系统栏

### 隐藏状态栏

本课程介绍如何在不同的 Android 版本上隐藏状态栏。隐藏状态栏（以及可选的导航栏）让内容使用更多的显示空间，从而提供更加身临其境的用户体验。

图 1 显示一个带状态栏的应用
![](https://developer.android.com/images/training/status_bar_show.png)

图 2 显示一个隐藏状态栏的应用。注意 action bar 也被隐藏了。不显示状态栏时 action bar 也不应该显示。
![](https://developer.android.com/images/training/status_bar_hide.png)

#### 在 Android 4.0 以及更低的版本上隐藏状态栏

你可以通过设置[WindowManager](https://developer.android.com/reference/android/view/WindowManager)flags 来在 Android4.0(API level 14)及以下隐藏状态栏。你可以编程方式或者在应用的清单文件中设置 activity 主体来实现它。如果状态栏需要一直在应用中隐藏，在应用清单文件中设置 activity 主题是首选的方式（尽管严格的说，如果你愿意你可以通过编程方式覆盖主题）。举例：

```xml
<application
    ...
    android:theme="@android:style/Theme.Holo.NoActionBar.Fullscreen" >
    ...
</application>
```

使用活动主题优点如下：

- 对比编程方式设置 flag，它更容易维护且更不容易出错
- 结果是 UI 转换更加平滑，因为系统在实例化应用的主 Activity 之前就获得了 UI 渲染的信息。

另外，你可以编程方式设置 WindowManager flags。这种方式使得用户和你应用交互的时候隐藏和显示状态栏更加方便。

```java
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // If the Android version is lower than Jellybean, use this call to hide
        // the status bar.
        if (Build.VERSION.SDK_INT < 16) {
            getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
                    WindowManager.LayoutParams.FLAG_FULLSCREEN);
        }
        setContentView(R.layout.activity_main);
    }
    ...
}
```

当你设置 WindowManager flags（无论是通过 activity 主题还是编程方式），除非你将它清除掉，否则它会一直保持影响。

你可以使用 FLAG_LAYOUT_IN_SCREEN 来设置你 Activity 布局，使其使用与启用 FLAG_FULLSCREEN 时可用的屏幕相同的样式。当状态栏隐藏或显示时，可以防止内容大小调整。

#### 在 Android4.1 及以上隐藏状态栏

你可以使用[setSystemUiVisibility()](<https://developer.android.com/reference/android/view/View#setSystemUiVisibility(int)>)在 Android4.1（API level 16）及以上隐藏状态栏。setSystemUiVisibility 在各个视图级别上设置 UI flags; 这些设置汇总到 window 的 level 上。使用 setSystemUiVisibility() 设置 UI flags 比使用 WindowManager flags 能够让你更加精细的控制你的系统栏。此代码段隐藏状态栏：

```java
View decorView = getWindow().getDecorView();
// 隐藏状态栏.
int uiOptions = View.SYSTEM_UI_FLAG_FULLSCREEN;
decorView.setSystemUiVisibility(uiOptions);
// 记住如果状态栏处于隐藏状态，那么action bar也应该隐藏
ActionBar actionBar = getActionBar();
actionBar.hide();
```

注意下面几点：

- 一旦 UI flags 清除，如果你想要再次隐藏栏，你的应用需要充值它们。看[Responding to UI Visibility Changes](https://developer.android.com/training/system-ui/visibility)对于如何监听 UI 可见性变化，以至于你应用应该如何响应的改变。
- 在不同位置设置 UI flags 会有些不同。如果你在 activity's 的 onCreate()方法中隐藏系统栏并且用户按下了 Home 键，这个系统栏将重新显示。当用户重新打开 activity，onCreate()方法不会再次调用，所以系统栏仍然可见。如果你想要在用户进出活动时系统 UI 的更改仍然存在，在 onResume()或者 onWindowFocusChanged()方法中设置 UI flags。
- setSystemUiVisibility()方法只有在调用的视图可见时生效。
- 离开视图，将清除使用 setSystemUiVisibility()设置的 flags。

#### 使内容显示在状态栏之下

在 Android4.1 及以上版本，你可以设置你应用的内容显示在状态栏之下，以便状态栏隐藏和显示时不会调整内容的大小。为此，请使用 SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN。你可能还需要使用 SYSTEM_UI_FLAG_LAYOUT_STABLE 来帮助你应用维护一个稳定的布局。

当你使用这种方法时，你有责任保证你应用的核心部分（举例，地图应用的内置组件）不会被系统栏覆盖。它可能会使得你的应用无法使用。大多数情况下你应该在你 XML 布局文件中添加 android:fitsSystemWindows 属性为 true。它会调整父 ViewGroup 的 padding 预留空间给系统 windows。这对大多数应用来说足够了。

但是，在大多数情况下，可能需要修改默认的 padding 来获得应用的理想布局。要直接操作你内容相对于系统栏的布局（占据被称为 window 的"content insets"),重写 fitSystemWindows(Rect insets)。 当 window 的 content insets 发生改变，视图的层级结构将调用 fitSystemWindows()方法，以允许 window 响应的调整内容。通过覆盖此方法你可以根据需要处理 insets（因此你应用的布局）。

### 隐藏导航栏

### 启用全屏模式

### 响应 UI 显示性变动

## 设计高效的导航

## 实现高效的导航

## 使用 ViewPager 在多个 fragments 之间滑动

## 支持 Swipe-to-Refresh

## Toast 概览

## Pop-up 消息概览

## Dialogs

## Menus

## 搜索概览

## Copy 和 Paste

## 拖拽和丢弃

## 创建向后兼容的 UI
