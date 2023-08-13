# Server (config/server.py)

`server` is the place to configure the server things.

`tools.sessions.on` <br>
**Default:** `True` <br>
**Description:** Enable a sessions

`engine.autoreload.on` <br>
**Default:** `False` <br>
**Description:** Auto Reload when source code change. Don't use it in production.

`request.show_tracebacks` <br>
**Default:** `False` <br>
**Description:** Show traceback for debugging in development purposes.

It have the configuration for the static error page for error code 403, 404, and 500 too.

    from config import directory

    update = {
      ...
      'error_page.403' : directory.erpadir(403),
      'error_page.404' : directory.erpadir(404),
      'error_page.500' : directory.erpadir(500),
      ...
    }
