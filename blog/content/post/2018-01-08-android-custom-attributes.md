---
title: Android Custom Attributes
author: Bill Farmer
type: post
date: 2018-01-08T11:02:04Z
url: /android-custom-attributes/
categories:
  - Hacking
keywords:
  - android
  - attributes
  - themes
---

I had resisted adding a dark theme to my [Diary][1] app until now
because it uses a [custom calendar][2] with built in customisable
styles and preferences with icons. So I had to work out how to make
the custom styles of the calendar revert to styles from the current
theme, and how to switch icons for the preferences according to the
theme.

### Calendar custom styles

The custom calendar has a list of styles that are customisable in a
`attrs.xml` file.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <declare-styleable name="CustomCalendarView">
    <attr name="calendarTitleTextColor" format="color" />
    <attr name="dayOfWeekTextColor" format="color" />
    <attr name="dayOfMonthTextColor" format="color" />
    <attr name="calendarBackgroundColor" format="color" />
    <attr name="disabledDayBackgroundColor" format="color" />
    <attr name="disabledDayTextColor" format="color" />
    <attr name="selectedDayBackground" format="reference" />
    <attr name="selectedDayTextColor" format="color" />
    <attr name="weekLayoutBackgroundColor" format="color" />
    <attr name="titleLayoutBackgroundColor" format="color" />
    <attr name="currentDayOfMonthColor" format="color" />
  </declare-styleable>
</resources>
```

The `CustomCalendarView` value is used in the sources to reference the
attributes.

```java
    // getAttributes
    private void getAttributes(AttributeSet attrs)
    {
        Resources resources = getResources();

        final TypedArray typedArray =
            context.obtainStyledAttributes(attrs, R.styleable
                                           .CustomCalendarView, 0, 0);
        calendarBackgroundColor =
            typedArray.getColor(R.styleable
                                .CustomCalendarView_calendarBackgroundColor,
                                resources.getColor(R.color.white));
        // ...
    }
```

These styles revert to a default value defined in the code if they are
not referenced externally. To revert to the current style defined
values, some of them need to be redefined to attribute values from the
style system.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:library="http://schemas.android.com/apk/res-auto"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    android:gravity="center">
  <org.billthefarmer.view.CustomCalendarView
      android:id="@+id/calendar"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:layout_margin="8dp"
      android:layout_gravity="center"
      library:calendarBackgroundColor="?android:attr/colorBackground"
      library:calendarTitleTextColor="?android:attr/textColorPrimary"
      library:dayOfMonthTextColor="?android:attr/textColorPrimary"
      library:dayOfWeekTextColor="?android:attr/textColorPrimary"
      library:disabledDayBackgroundColor="?android:attr/colorBackground"
      library:disabledDayTextColor="?android:attr/textColorTertiary"
      library:titleLayoutBackgroundColor="?android:attr/colorBackground"
      library:weekLayoutBackgroundColor="?android:attr/colorBackground" />
</LinearLayout>
```

I was not able to find a suitable value for the
`disabledDayBackgroundColor` style, so left it as normal
background. After these changes the calendar styles match the rest of
the app.

### Styleable icons

The android preference system uses optional icons which are displayed
on the preference screen, dark icons on a light background, light
icons on a dark background. To allow the icons to be styleable requires
another `attrs.xml` file. Again the `name` value appears to be ignored.

```xml
<?xml version="1.0" encoding="utf-8"?>
resources>
  <declare-styleable name="Diary">
    <attr name="pref_calendar" format="reference" />
    <attr name="pref_markdown" format="reference" />
    <attr name="pref_folder" format="reference" />
    <attr name="pref_copy_media" format="reference" />
    <attr name="pref_dark_theme" format="reference" />
    <attr name="pref_about" format="reference" />
  </declare-styleable>
</resources>
```

To define values for these attributes for each theme requires a list of items for each style.

```xml
  <!-- Application theme. -->
  <style name="AppTheme" parent="AppBaseTheme">
    <!--
        All customizations that are NOT specific to a particular
        API-level can go here.
    -->
    <item name="pref_calendar">@drawable/ic_pref_calendar</item>
    <item name="pref_markdown">@drawable/ic_pref_markdown</item>
    <item name="pref_folder">@drawable/ic_pref_folder</item>
    <item name="pref_copy_media">@drawable/ic_pref_copy_media</item>
    <item name="pref_dark_theme">@drawable/ic_pref_dark_theme</item>
    <item name="pref_about">@drawable/ic_pref_about</item>
  </style>

  <!-- Application theme. -->
  <style name="AppDarkTheme" parent="AppDarkBaseTheme">
    <!--
        All customizations that are NOT specific to a particular
        API-level can go here.
    -->
    <item name="pref_calendar">@drawable/ic_pref_calendar_dark</item>
    <item name="pref_markdown">@drawable/ic_pref_markdown_dark</item>
    <item name="pref_folder">@drawable/ic_pref_folder_dark</item>
    <item name="pref_copy_media">@drawable/ic_pref_copy_media_dark</item>
    <item name="pref_dark_theme">@drawable/ic_pref_dark_theme_dark</item>
    <item name="pref_about">@drawable/ic_pref_about_dark</item>
  </style>
```

To complete this requires the icon references in the `preferences.xml`
file to be changed from a `@drawable` reference to an `?attr` reference.

```xml

<CheckBoxPreference
	android:key="pref_custom"
	android:icon="?attr/pref_calendar"
	android:title="@string/pref_calendar"
	android:summary="@string/pref_calendar_summ"
	android:persistent="true"
	android:defaultValue="true" />

<CheckBoxPreference
	android:key="pref_markdown"
	android:icon="?attr/pref_markdown"
	android:title="@string/pref_markdown"
	android:summary="@string/pref_markdown_summ"
	android:persistent="true"
	android:defaultValue="true" />

<EditTextPreference
    android:key="pref_folder"
	android:icon="?attr/pref_folder"
	android:title="@string/folder"
	android:persistent="true"
	android:defaultValue="Diary" />

<CheckBoxPreference
	android:key="pref_copy_media"
	android:icon="?attr/pref_copy_media"
	android:title="@string/pref_copy_media"
	android:summary="@string/pref_copy_media_summ"
	android:persistent="true"
	android:defaultValue="false" />

<CheckBoxPreference
	android:key="pref_dark_theme"
	android:icon="?attr/pref_dark_theme"
	android:title="@string/pref_dark_theme"
	android:summary="@string/pref_dark_theme_summ"
	android:persistent="true"
	android:defaultValue="false" />

<org.billthefarmer.diary.AboutPreference
	android:key="pref_about"
	android:icon="?attr/pref_about"
	android:dialogIcon="?attr/pref_about"
	android:negativeButtonText=""
	android:title="@string/about"
	android:summary="@string/pref_about_summ"
	android:dialogLayout="@layout/about" />
```

This is all that is required, after these changes the icons switch as
the theme is changed.

[1]: https://github.com/billthefarmer/diary
[2]: https://github.com/billthefarmer/CustomCalendarView
