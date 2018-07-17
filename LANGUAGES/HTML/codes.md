# **HTML**
Some HTML useful codes:

### Force no cache
##### Just some metatags to force no cache

```html
<meta http-equiv="cache-control" content="max-age=0" />
<meta http-equiv="cache-control" content="no-cache" />
<meta http-equiv="expires" content="0" />
<meta http-equiv="expires" content="Tue, 01 Jan 1980 1:00:00 GMT" />
<meta http-equiv="pragma" content="no-cache" />
```

-------

### Default image on error 404
##### Force a default image when the image uri is not found (Error 404)

```html
<!-- HTML only solutions (check browser support of object element) -->
<object data="if-does-not-exist.png" type="image/png">
  <img src="this-will-be-the-default.png" />
</object>

<!-- Using a JS inline -->
<img src="if-does-not-exist.png" alt="" onerror="this.onerror=null;this.src='this-will-be-the-default.png';" />

```

-------
