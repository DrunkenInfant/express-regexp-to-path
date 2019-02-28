# express-regexp-to-path

Create path pattern from express router stack and url, reverse https://github.com/pillarjs/path-to-regexp

## Limitations

The url `/foo/foo` with registered middleware at `/foo/:id` will incorrectly be constructed as `/:id/foo`.

It is impossible to actually test if `next()` was called from a middleware, the returned pattern might be longer than
the called stack. Eg:

```
app.use('/foo', (req, res, next) => {
  if (arbitrary) {
    next();
  } else {
    res.send("Not today!");
  }
});

app.get('/foo/:id', (req, res) => {
  res.send("Enter!");
});
```

If the url is `/foo/1` it is imossilbe to know if the registered middleware at `/foo/:id` was called for this request.
The returned pattern will be `/foo/:id` regardless.

## Example

```
const getPath = require('express-regexp-to-path'),
      app = require('express')();

app.use((req, res, next) => {
  let { pattern, matched } = getPath(req, req.app._router);
  if (matched) {
    Logger.info(`Matched path: ${pattern}`);
  }
});

app.get('/foo/:id', (req, res) => {
  res.send('OK');
});
```
