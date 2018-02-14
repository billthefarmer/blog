---
title: "Adding a calendar event in android"
author: Bill Farmer
date: 2017-09-05T14:54:25+01:00
url: /adding-a-calendar-event/
categories:
  - Hacking
keywords:
  - android
  - calendar
  - event
  - reminder
  - asynchronous
---

The android docs for the [Calendar Provider][1] are quite useful, but
recommend the use of an [AsyncQueryHandler][2] for querying or adding
events to calendars in an asynchronous thread. However the
`AsyncQueryHandler` docs don't include much guidance as to how to use
it.

To add an entry to an android calendar you first have to find out the
calendar id using an asynchronous query on the calendars table.

```java
// QueryHandler
public class QueryHandler extends AsyncQueryHandler
{
    private static final String TAG = "QueryHandler";

    // Projection arrays
    private static final String[] CALENDAR_PROJECTION = new String[]
        {
            Calendars._ID
        };

    // The indices for the projection array above.
    private static final int CALENDAR_ID_INDEX = 0;

    private static final int CALENDAR = 0;
    private static final int EVENT    = 1;
    private static final int REMINDER = 2;

    private static QueryHandler queryHandler;

    // QueryHandler
    public QueryHandler(ContentResolver resolver)
    {
        super(resolver);
    }

    // insertEvent
    public static void insertEvent(Context context, long startTime,
                                   long endTime, String title)
    {
        ContentResolver resolver = context.getContentResolver();

        if (queryHandler == null)
            queryHandler = new QueryHandler(resolver);

        ContentValues values = new ContentValues();
        values.put(Events.DTSTART, startTime);
        values.put(Events.DTEND, endTime);
        values.put(Events.TITLE, title);

        if (BuildConfig.DEBUG)
            Log.d(TAG, "Calendar query start");

        queryHandler.startQuery(CALENDAR, values, Calendars.CONTENT_URI,
                                CALENDAR_PROJECTION, null, null, null);
    }

```

This method is called from an app activity.

```java
        QueryHandler.insertEvent(this, startTime.getTimeInMillis(),
                                 endTime.getTimeInMillis(), title);

```

The `insertEvent()` method first creates a new `QueryHandler` which is
subsequently reused. The start time, end time and title of the event
are added to a `ContentValues` object and passed to the `startQuery()`
method. This starts an asynchronous query on the Calendars table to
find the first calendar id. A database cursor and the values are
passed to the `onQueryComplete()` method.

```java
    // onQueryComplete
    @Override
    public void onQueryComplete(int token, Object object, Cursor cursor)
    {
        // Use the cursor to move through the returned records
        cursor.moveToFirst();

        // Get the field values
        long calendarID = cursor.getLong(CALENDAR_ID_INDEX);

        if (BuildConfig.DEBUG)
            Log.d(TAG, "Calendar query complete " + calendarID);

        ContentValues values = (ContentValues) object;
        values.put(Events.CALENDAR_ID, calendarID);
        values.put(Events.EVENT_TIMEZONE,
                   TimeZone.getDefault().getDisplayName());

        startInsert(EVENT, null, Events.CONTENT_URI, values);
    }
```

This gets the calendar id from the cursor and adds it to the values
along with the timezone. It's probably not necessary to query the
first calendar id because the value seems to be one, but there may be
instances where it isn't. The `startInsert()` method inserts the event
asynchronously and the result is passed to the `onInsertComplete()`
method.

```java
    // onInsertComplete
    @Override
    public void onInsertComplete(int token, Object object, Uri uri)
    {
        if (uri != null)
        {
            if (BuildConfig.DEBUG)
                Log.d(TAG, "Insert complete " + uri.getLastPathSegment());

            switch (token)
            {
            case EVENT:
                long eventID = Long.parseLong(uri.getLastPathSegment());
                ContentValues values = new ContentValues();
                values.put(Reminders.MINUTES, 10);
                values.put(Reminders.EVENT_ID, eventID);
                values.put(Reminders.METHOD, Reminders.METHOD_ALERT);
                startInsert(REMINDER, null, Reminders.CONTENT_URI, values);
                break;
            }
        }
    }
```

This gets the event id and inserts a 10 minute reminder. All the
`AsyncQueryHandler` asynchronous methods include an integer and an
`Object` in the argument list which are passed to the `onComplete()`
methods. This is useful for passing on parameters to be used later.

```shell
09-11 10:27:56.518  9808  9808 D QueryHandler: Calendar query start
09-11 10:27:56.687  9808  9808 D QueryHandler: Calendar query complete 1
09-11 10:27:56.801  9808  9808 D QueryHandler: Insert complete 112
09-11 10:27:56.944  9808  9808 D QueryHandler: Insert complete 1
```

Android takes a while to add the event as can be seen from the log.

[1]: https://developer.android.com/guide/topics/providers/calendar-provider.html
[2]: https://developer.android.com/reference/android/content/AsyncQueryHandler.html
