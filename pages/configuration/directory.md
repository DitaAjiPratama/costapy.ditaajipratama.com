# Directory (config/directory.py)

`directory.py` is the place for storing your path. It is useful to calling the path more efficiently. there is 2 method that you can store your path. store it in variable for templating configuration, and store it as object for routing the url.

This is for a static error pages. This variable will be use in `server` configuration.

    def erpadir(err):
      return f'static/error/{err}.html'

This is example that use for templating. This variable will be use in `template` configuration.

    user_page       = "static/user/page"
    user_template   = "static/user/template"

    admin_page      = "static/admin/page"
    admin_template  = "static/admin/template"

    email_page      = "static/email/page"
    email_template  = "static/email/template"

And this is example that use for routing the url

    dirconfig = {
        '/' :
        {
            'tools.sessions.on'     : True ,
            'tools.staticdir.root'  : os.path.abspath(os.getcwd()) ,
        },
        '/your_dir' :
        {
            'tools.staticdir.on'    : True ,
            'tools.staticdir.dir'   : './static/your-dir' ,
        },
    }
