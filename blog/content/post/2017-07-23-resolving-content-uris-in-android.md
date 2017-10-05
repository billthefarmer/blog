---
title: Resolving content uris in android
author: Bill Farmer
type: post
date: 2017-07-23T10:48:17+00:00
url: /resolving-content-uris-in-android/
categories:
  - Hacking
keywords:
  - android
  - content
  - uri
  - file
---
If you write an android app that handles images or any other sort of file your app may be required to deal with &lsquo;content&rsquo; uris (`content://`). There is a very useful utility which resolves these into &lsquo;file&rsquo; uris (`file:///`) [FileUtils.java][1]. This contains one external reference to [LocalStorageProvider][2], which can be resolved by removing the function `isLocalStorageDocument()` and references to it.

```java
    // import com.ianhanniballake.localstorage.LocalStorageProvider;
    // ...
        /**
         * @param uri The Uri to check.
         * @return Whether the Uri authority is {@link LocalStorageProvider}.
         * @author paulburke
         */
        public static boolean isLocalStorageDocument(Uri uri) {
            return LocalStorageProvider.AUTHORITY.equals(uri.getAuthority());
        }
```

Having done that, you can then resolve &lsquo;content&rsquo; uris

```java
    public final static String CONTENT = "content";
    // ...
            if (uri.getScheme().equalsIgnoreCase(CONTENT))
                uri = resolveContent(uri);
    // ...
        // resolveContent
        private Uri resolveContent(Uri uri)
        {
            String path = FileUtils.getPath(this, uri);
            if (path != null)
            {
                File file = new File(path);
                if (file.canRead())
                    uri = Uri.fromFile(file);
            }
            return uri;
        }
```

This may not always work, but I haven&rsquo;t been able to defeat it. Later versions of android have added a &lsquo;Documents&rsquo; folder, which is encoded as &lsquo;home&rsquo;, so some additional code is required to resolve it.

```java
    /**
     * Get a file path from a Uri. This will get the the path for
     * Storage Access Framework Documents, as well as the _data field
     * for the MediaStore and other file-based ContentProviders.<br>
     * <br>
     * Callers should check whether the path is local before assuming
     * it represents a local file.
     *
     * @param context The context.
     * @param uri The Uri to query.
     * @see #isLocal(String)
     * @see #getFile(Context, Uri)
     * @author paulburke
     */
    @TargetApi(19)
    public static String getPath(final Context context, final Uri uri)
    {
        // ...
        final boolean isKitKat =
            Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT;

        // DocumentProvider
        if (isKitKat && DocumentsContract.isDocumentUri(context, uri))
        {
            // ExternalStorageProvider
            if (isExternalStorageDocument(uri))
            {
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];

                // /
                if ("primary".equalsIgnoreCase(type))
                {
                    return Environment
                        .getExternalStorageDirectory() + "/" + split[1];
                }

                // /Documents
                else if ("home".equalsIgnoreCase(type))
                {
                    return Environment
                        .getExternalStorageDirectory() + "/Documents/" +
                        split[1];
                }
                // TODO handle non-primary volumes
            }
        // ...
        }
    // ...
    }
```

 [1]: https://github.com/iPaulPro/aFileChooser/blob/master/aFileChooser/src/com/ipaulpro/afilechooser/utils/FileUtils.java
 [2]: https://github.com/iPaulPro/aFileChooser/blob/master/aFileChooser/src/com/ianhanniballake/localstorage/LocalStorageProvider.java
