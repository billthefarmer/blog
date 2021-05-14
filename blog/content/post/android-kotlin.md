---
title: Android Kotlin
author: Bill Farmer
type: post
date: 2021-05-14T09:49:11+01:00
url: /android-kotlin/
categories:
    - Hacking
keywords:
    - android
    - java
    - kotlin
---

All my android apps have up until now been written in Java, avoiding
the use of the support libraries or [androidx][1]. The Google
developers docs and [guides][2] advocate the use of [Kotlin][3] rather
than Java and the use of androidx almost to the exclusion of the
standard android API.

I have a very simple app, [Oglaf][4], NS(Generally)FW, which is just a
wrapper around the web site, with some navigation. So I decided to
convert it to Kotlin, just to see how difficult it was and to see what
effect, if any, it had on the app.

The obvious differences with java are declaration of variables and
classes, and not needing semicolons on the end of statements.

```java
// Java
package org.billthefarmer.oglaf;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.Dialog;
import android.content.Intent;
import android.os.Bundle;
import android.text.method.LinkMovementMethod;
import android.text.SpannableStringBuilder;
import android.view.Menu;
import android.view.MenuItem;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;
import android.widget.TextView;

import java.text.DateFormat;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Oglaf extends Activity
{
    public static final String URL = "https://oglaf.com";
    public static final String TEXT_PLAIN = "text/plain";

    private WebView webView;

    // Called when the activity is first created.
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        webView = findViewById(R.id.webview);
        if (webView != null)
        {
            // Enable javascript, Oglaf doesn't work unless JavaScript
            // is enabled
            WebSettings settings = webView.getSettings();
            settings.setJavaScriptEnabled(true);

            // Enable zoom
            settings.setBuiltInZoomControls(true);
            settings.setDisplayZoomControls(false);
```

```kotlin
// Kotlin
package org.billthefarmer.oglaf

import android.app.Activity
import android.app.AlertDialog
import android.app.Dialog
import android.content.Intent
import android.os.Bundle
import android.text.method.LinkMovementMethod
import android.text.SpannableStringBuilder
import android.view.Menu
import android.view.MenuItem
import android.webkit.WebSettings
import android.webkit.WebView
import android.webkit.WebViewClient
import android.widget.TextView

import java.text.DateFormat

import java.util.regex.Matcher
import java.util.regex.Pattern

class Oglaf: Activity()
{
    val URL = "https://oglaf.com"
    val TEXT_PLAIN = "text/plain"

    lateinit var webView: WebView

    // Called when the activity is first created.
    override fun onCreate(savedInstanceState: Bundle?)
    {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main)
        webView = findViewById(R.id.webview)
        // Enable javascript, Oglaf doesn't work unless JavaScript
        // is enabled
        val settings  = webView.getSettings()
        settings.setJavaScriptEnabled(true)

        // Enable zoom
        settings.setBuiltInZoomControls(true)
        settings.setDisplayZoomControls(false)
```

There are nuances like declaring the web view `lateinit` because it is
initialised in `onCreate()`.

The next bit wasn't so easy. I had to search extensively to find the
correct syntax for what Kotlin calls an [object expression][5].

```java
// Java
            // Follow links and set title
            webView.setWebViewClient(new WebViewClient()
            {
                // onPageFinished
                @Override
                public void onPageFinished(WebView view, String url)
                {
                    // Get page title
                    if (view.getTitle() != null)
                        setTitle(view.getTitle());

                    if (view.canGoBack())
                        getActionBar().setDisplayHomeAsUpEnabled(true);

                    else
                        getActionBar().setDisplayHomeAsUpEnabled(false);
                }
            });
```

```kotlin
// Kotlin
        // Follow links and set title
        webView.setWebViewClient(object: WebViewClient()
        {
            // onPageFinished
            override fun onPageFinished(view: WebView, url: String)
            {
                // Get page title
                if (view.getTitle() != null)
                setTitle(view.getTitle())

                if (view.canGoBack())
                getActionBar()?.setDisplayHomeAsUpEnabled(true)

                else
                getActionBar()?.setDisplayHomeAsUpEnabled(false)
            }
        })
```

The other significant difference is that a case statement becomes a
when statement.

```java
// Java
        // Get id
        int id = item.getItemId();
        switch (id)
        {
            // Home
        case android.R.id.home:
            // Back navigation
            if (webView != null && webView.canGoBack())
                webView.goBack();
            else
                finish();
            break;

            // Refresh
        case R.id.action_refresh:
            refresh();
            break;

            // Share
        case R.id.action_share:
            share();
            break;

            // About
        case R.id.action_about:
            about();
            break;

        default:
            return false;
        }
```

```kotlin
// Kotlin
        // Get id
        val id = item.getItemId()
        when (id)
        {
            // Home
            android.R.id.home ->
            // Back navigation
            if (webView.canGoBack())
                webView.goBack()
            else
                finish()

            // Refresh
            R.id.action_refresh ->
            refresh()

            // Share
            R.id.action_share ->
            share()

            // About
            R.id.action_about ->
            about()

        else ->
            return false
        }
```

The obvious difference with the app is that the Java version size is
74Kb, the Kotlin version is 781Kb. Rather a lot of bloat for a small
simple app.

[1]: https://developer.android.com/jetpack/androidx
[2]: https://developer.android.com/guide
[3]: https://developer.android.com/kotlin
[4]: https://github.com/billthefarmer/oglaf
[5]: https://kotlinlang.org/docs/object-declarations.html

