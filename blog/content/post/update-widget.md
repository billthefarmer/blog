---
title: Update App Widget
author: Bill Farmer
type: post
date: 2023-07-29T19:33:44+01:00
url: /update-widget/
categories:
    - Hacking
keywords:
    - android
    - widget
    - service
---

Android app widgets normally can be set up to update periodically, but
not repeatedly for a short period. For a [bus times app][1] I need the
widget to update every 30 seconds for about 5 minutes.

I already had a [Service][2] set up to update the widget from a
suitable web site and using a [Handler][3] to reset the progress bar
if the background update process failed.

```java
    public static final int RESET_DELAY = 5 * 1000;
    public static final int REPEAT_DELAY = 30 * 1000;
    public static final int STOP_DELAY = 5 * 60 * 1000;

    private Handler handler;
    private boolean stopUpdate;

    // onCreate
    @Override
    public void onCreate()
    {
        // Get handler
        handler = new Handler(Looper.getMainLooper());

        if (BuildConfig.DEBUG)
            Log.d(TAG, "onCreate " + handler);
    }
```
Define the various delays and get a handler in the `onCreate()` method.

```java
    // onStartCommand
    @Override
    @SuppressWarnings("deprecation")
    public int onStartCommand(Intent intent, int flags, int startId)
    {
        if (BuildConfig.DEBUG)
            Log.d(TAG, "onStartCommand " + intent);

        startBusesUpdate();

        handler.postDelayed(() ->
        {
            stopUpdate = true;
            stopSelf();
        }, STOP_DELAY);

        return START_NOT_STICKY;
    }
```

I discovered that calling `stopSelf()` on the service didn't stop the
periodic updates, so I used a flag to stop them.

```java
        // Get the layout for the widget
        RemoteViews views = new
            RemoteViews(getPackageName(), R.layout.widget);
        views.setViewVisibility(R.id.refresh, View.INVISIBLE);
        views.setViewVisibility(R.id.progress, View.VISIBLE);

        // Get manager
        AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(this);
        ComponentName provider = new
            ComponentName(this, BusesWidgetProvider.class);

        int appWidgetIds[] = appWidgetManager.getAppWidgetIds(provider);
        appWidgetManager.partiallyUpdateAppWidget(appWidgetIds, views);

        handler.postDelayed(() ->
        {
            views.setViewVisibility(R.id.refresh, View.VISIBLE);
            views.setViewVisibility(R.id.progress, View.INVISIBLE);
            appWidgetManager.partiallyUpdateAppWidget(appWidgetIds, views);
        }, RESET_DELAY);
```

After starting the update process set the progress bar visible and set
up a delayed reset in case the update failed.

```java
            // Get manager
            AppWidgetManager appWidgetManager =
                AppWidgetManager.getInstance(this);
            ComponentName provider = new
                ComponentName(this, BusesWidgetProvider.class);

            int appWidgetIds[] = appWidgetManager.getAppWidgetIds(provider);
            Intent broadcast = new
                Intent(AppWidgetManager.ACTION_APPWIDGET_UPDATE);
            broadcast.putExtra(AppWidgetManager.EXTRA_APPWIDGET_IDS,
                               appWidgetIds);
            broadcast.putExtra(EXTRA_UPDATE_DONE, true);
            sendBroadcast(broadcast);

            if (BuildConfig.DEBUG)
                Log.d(TAG, "Broadcast " + broadcast);

            if (!stopUpdate)
                handler.postDelayed(() ->
                    startBusesUpdate(), REPEAT_DELAY);
```

After a successful update send a broadcast to update the widget and a
delayed method call to do it again unless the flag is set.

From android 10 there are restrictions on what background processes
can do to start other processes. To work around this create an
activity with the **Theme.NoDisplay** theme just to start the service.
```java
    // On create
    @Override
    @SuppressWarnings("deprecation")
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);

        if (BuildConfig.DEBUG)
            Log.d(TAG, "onCreate " + getIntent());

        // Start update service
        Intent update = new Intent(this, BusesWidgetUpdate.class);
        startService(update);

        if (BuildConfig.DEBUG)
            Log.d(TAG, "Update " + update);

        finish();
    }
```

 [1]: https://github.com/billthefarmer/buses
 [2]: https://developer.android.com/reference/android/app/Service
 [3]: https://developer.android.com/reference/android/os/Handler
