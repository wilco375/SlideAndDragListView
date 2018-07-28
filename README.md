# SlideAndDragListView

  [![License](http://img.shields.io/:license-apache-blue.svg)](LICENSE.txt)    [![API](https://img.shields.io/badge/API-11%2B-brightgreen.svg?style=flat)](https://android-arsenal.com/api?level=11)

A ListView that you can slide ( or swipe ) the items, drag and drop the items to other places.

![sdlv](https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/v1.1.gif)

中文：[CHINESE.md](https://github.com/yydcdut/SlideAndDragListView/blob/master/CHINESE.md)

Demo: [DOWNLOAD](https://github.com/yydcdut/SlideAndDragListView/blob/master/apk/sdlv.apk?raw=true)

ChangeLog: [CHANGELOG.md](https://github.com/yydcdut/SlideAndDragListView/blob/master/CHANGELOG.md)

# Overview

SlideAndDragListView (SDLV) is an extension of the Android ListView that enables slide and drag-and-drop reordering of list items.

 Some key features are:

1. Clean drag and drop.
2. Intuitive and smooth scrolling while dragging or sliding.
3. Support onItemClick and onItemLongClick listener.
4. Public callback methods.
5. Two side of item can slide.
6. so on...

SlideAndDragListView is useful for all kinds of prioritized lists: favorites, playlists, checklists, etc. Would love to hear about your use case or app by email. I hope you find it useful; and please, help me improve the thing!

# Binaries

## Gradle

Root `build.gradle`
``` groovy
allprojects {
	repositories {
		...
		maven { url 'https://jitpack.io' }
	}
}
```

App `build.gradle`
``` groovy
dependencies {
	implementation 'com.github.wilco375:SlideAndDragListView:0.7.9'
}
```

# Widget Usage

## Menu Item Click & Slide Directions

### Step 1

- Add SlideAndDragListView in layout xml

``` xml
<com.yydcdut.sdlv.SlideAndDragListView
        android:layout_width="match_parent"
        android:layout_height="match_parent">
</com.yydcdut.sdlv.SlideAndDragListView>
```

### Step 2

- Create a `Menu` and add `MenuItem`

``` java
Menu menu = new Menu(true, 0);//the first parameter is whether can slide over
menu.addItem(new MenuItem.Builder().setWidth(90)//set Width
                .setBackground(new ColorDrawable(Color.RED))// set background
                .setText("One")//set text string
                .setTextColor(Color.GRAY)//set text color
                .setTextSize(20)//set text size
                .setIcon(getResources().getDrawable(R.drawable.ic_launcher))// set icon
                .build());
menu.addItem(new MenuItem.Builder().setWidth(120)
                .setBackground(new ColorDrawable(Color.BLACK))
                .setDirection(MenuItem.DIRECTION_RIGHT)//set direction (default DIRECTION_LEFT)
                .setIcon(getResources().getDrawable(R.drawable.ic_launcher))// set icon
                .build());
//set in sdlv
slideAndDragListView.setMenu(menu);
```

The class `Menu`, the construct function `Menu(boolean slideOver, int menuViewType)`,  the first parameter means whether can slide over.

If it’s `true`:

<img width="350" height="70" src="https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/wannaOver_true.gif" />

If it’s `false`:

<img width="350" height="70" src="https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/wannaOver_false.gif" />

The second parameter stands for view type, the value of `int getItemViewType(int)` in `BaseAdapter`.

### Step 3

- Implement menu item click listener

``` java
slideAndDragListView.setOnMenuItemClickListener(new OnMenuItemClickListener() {
    @Override
    public int onMenuItemClick(View v, int itemPosition, int buttonPosition, int direction) {
        switch (direction) {
            case MenuItem.DIRECTION_LEFT:
                switch (buttonPosition) {
                    case 0://One
                        return Menu.ITEM_SCROLL_BACK;
                }
                break;
            case MenuItem.DIRECTION_RIGHT:
                switch (buttonPosition) {
                    case 0://icon
                        return Menu.ITEM_DELETE_FROM_BOTTOM_TO_TOP;
                }
                break;
            default :
                return Menu.ITEM_NOTHING;
        }
        return Menu.ITEM_NOTHING;
    }
});
```

`Menu.ITEM_NOTHING`:

<img width="350" height="70" src="https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/ITEM_NOTHING.gif" />

`Menu.ITEM_SCROLL_BACK`:

<img width="350" height="70" src="https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/ITEM_SCROLL_BACK.gif" />

`Menu.ITEM_DELETE_FROM_BOTTOM_TO_TOP`:

<img width="350" height="70" src="https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/ITEM_DELETE_FROM_BOTTOM_TO_TOP.gif" />

## Create Different Menu

* Use the ViewType of adapter

``` java
private BaseAdapter mAdapter = new BaseAdapter() {
        // .......
        @Override
        public int getItemViewType(int position) {
            return position % 2;//current menu type
        }

        @Override
        public int getViewTypeCount() {
            return 2;//menu type count
        }
  		// ......
}
```

* Create different menus depending on the view type

``` java
List<Menu> menuList = new ArrayList<>(2);
Menu menu0 = new Menu(new ColorDrawable(Color.WHITE), true, 0);
menu0.addItem(new MenuItem.Builder().setWidth(90)//set Width
                .setBackground(new ColorDrawable(Color.RED))// set background
                .setText("One")//set text string
                .setTextColor(Color.GRAY)//set text color
                .setTextSize(20)//set text color
                .build());
menu0.addItem(new MenuItem.Builder().setWidth(120)
                .setBackground(new ColorDrawable(Color.BLACK))
                .setDirection(MenuItem.DIRECTION_RIGHT)//set direction (default DIRECTION_LEFT)
                .setIcon(getResources().getDrawable(R.drawable.ic_launcher))// set icon
                .build());
Menu menu1 = new Menu(new ColorDrawable(Color.YELLOW), false, 1);
menu1.addItem(new MenuItem.Builder().setWidth(60)
                .setBackground(new ColorDrawable(Color.RED))
                .setText("Two")
                .setTextColor(Color.GRAY)
                .setTextSize(25)
                .build());
menu1.addItem(new MenuItem.Builder().setWidth(70)
                .setBackground(new ColorDrawable(Color.BLUE))
                .setText("Three")
                .setDirection(MenuItem.DIRECTION_RIGHT)
                .setTextColor(Color.BLACK)
                .setTextSize(20)
                .build());
menuList.add(menu0);
menuList.add(menu1);
listView.setMenu(menuList)
```

* See the demo

<img width="350" height="140" src="https://raw.githubusercontent.com/yydcdut/SlideAndDragListView/master/gif/deferrentMenu.gif" />

## Drag

``` java
ApplicationInfo mDraggedEntity;
List<ApplicationInfo> mDataList;

// ...init...

slideAndDragListView.setOnDragDropListener(new OnDragDropListener() {
    @Override
    public void onDragViewStart(int beginPosition) {
        mDraggedEntity = mDataList.get(beginPosition);
    }

    @Override
    public void onDragDropViewMoved(int fromPosition, int toPosition) {
		ApplicationInfo applicationInfo = mDataList.remove(fromPosition);
      	mDataList.add(toPosition, applicationInfo);
    }

    @Override
    public void onDragViewDown(int finalPosition) {
		mDataList.set(finalPosition, mDraggedEntity);
    }
});
```

`public void onDragViewStart(int position)`.The parameter `position` is the position in ListView where dragged from.

`public void onDragDropViewMoved(int fromPosition, int toPosition)` .The parameters `fromPosition` and `toPosition` are the positions in ListView where dragged from and dragged to.

`public void onDragViewDown(int position)` . The parameter `position` is the position in ListView where dropped down.

## Other Listeners

### Item Slide Listener

``` java
slideAndDragListView.setOnSlideListener(new OnSlideListener() {
    @Override
    public void onSlideOpen(View view, View parentView, int position, int direction) {
        
    }

    @Override
    public void onSlideClose(View view, View parentView, int position, int direction) {

    }
});
```

`public void onSlideOpen(View view, View parentView, int position, int direction)`. The parameter `view` is the ListView item that is slide open, `parentView` here is SDLV, `position` is the position of the view in the list, and the parameter `direction` is the item slided direction.

`public void onSlideClose(View view, View parentView, int position, int direction)`. The parameter `view` is the ListView item that is slide close,`parentView` here is SDLV, `position` is the position of the view in the list, and the parameter `direction` is the item slided direction.

### Item Delete Listener

``` java
slideAndDragListView.setOnItemDeleteListener(new OnItemDeleteListener() {
    @Override
    public void onItemDeleteAnimationFinished(View view, int position) {
        
    }
});
```

`public void onItemDeleteAnimationFinished(View view, int position)` will invoked after `int onMenuItemClick(View v, int itemPosition, int buttonPosition, int direction)` return `Menu.ITEM_DELETE_FROM_BOTTOM_TO_TOP`.

### Item Menu Scroll Back Listener

```java
slideAndDragListView.setOnItemScrollBackListener(new OnItemScrollBackListener() {
    @Override
    public void onScrollBackAnimationFinished(View view, int position) {

    }
});
```

`public void onScrollBackAnimationFinished(View view, int position)` will invoked after `int onMenuItemClick(View v, int itemPosition, int buttonPosition, int direction)` return `Menu.ITEM_SCROLL_BACK`.

## API

### Slide(Open) Menu

```java
slideAndDragListView.slideItem(position, direction)
```

slide(open) menu by API.

### Close Menu

```java
slideAndDragListView.closeSlidedItem();
```

Close menu by API.

### Delete Menu

```java
slideAndDragListView.deleteSlideItem();
```

Delete menu by API.

### Drag

```java
slideAndDragListView.startDrag(position);
```

Drag list item.

### Not Drag headers or footers view types

```java
slideAndDragListView.setNotDragHeaderCount(1);
slideAndDragListView.setNotDragFooterCount(1);
```

See more: [HeaderFooterViewTypeActivity.java](https://github.com/yydcdut/SlideAndDragListView/blob/master/app/src/main/java/com/yydcdut/demo/HeaderFooterViewTypeActivity.java)

# License

Copyright 2015 yydcdut

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.