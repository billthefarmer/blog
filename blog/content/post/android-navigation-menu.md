---
title: Android Navigation Menu
author: Bill Farmer
type: post
date: 2023-09-06T13:46:00+01:00
url: /android-navigation-menu/
categories:
    - Hacking
keywords:
    - android
    - navigation
    - menu
---

Android versions from android 5 use a [Toolbar][1] as the
[ActionBar][2], but there appears to be no obvious API access.

However, by traversing the view hierarchy, it can be found and used to
set up app navigation.

![Navigation](images/2023/09/Navigation.png)

Create a recursive function to traverse the views and find the toolbar.
```java
    // findToolbar
    private Toolbar findToolbar(ViewGroup group)
    {
        View result = null;
        final int count = group.getChildCount();
        for (int i = 0; i < count; i++)
        {
            View view = group.getChildAt(i);
            if (view instanceof Toolbar)
                return (Toolbar) view;

            if (view instanceof ViewGroup)
                result = findToolbar((ViewGroup) view);

            if (result != null)
                break;
        }

        return (Toolbar) result;
    }
```
Set up navigation on the toolbar.
```java
        // Find toolbar
        ViewGroup root = (ViewGroup) getWindow().getDecorView();
        toolbar = findToolbar(root);

        // Set up navigation
        toolbar.setNavigationIcon(R.drawable.ic_menu_white_36dp);
        toolbar.setNavigationOnClickListener((v) ->
        {
            PopupMenu popup = new PopupMenu(this, v);
            popup.inflate(R.menu.navigation);
            popup.setOnMenuItemClickListener(this);
            popup.show();
        });
```
Create a menu.
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
      android:id="@+id/app_name"
      android:title="@string/short_name" />
  <item
      android:id="@+id/action_spectrum"
      android:title="@string/action_spectrum" />
  <item
      android:id="@+id/action_help"
      android:title="@string/action_help" />
  <item
      android:id="@+id/action_settings"
      android:title="@string/action_settings" />
</menu>
```
And a menu click listener.
```java
    // onMenuItemClick
    @Override
    public boolean onMenuItemClick(MenuItem item)
    {
        // Get id
        int id = item.getItemId();
        switch (id)
        {
        // Spectrum
        case R.id.action_spectrum:
            return onSpectrumClick(item);

        // Help
        case R.id.action_help:
            return onHelpClick(item);

        // Settings
        case R.id.action_settings:
            return onSettingsClick(item);

        default:
            return false;
        }
    }
```

 [1]: https://developer.android.com/reference/android/widget/Toolbar?hl=en
 [2]: https://developer.android.com/reference/android/app/ActionBar?hl=en
