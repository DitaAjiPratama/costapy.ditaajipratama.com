# Handling the modules

## Import the modules

    import modules.api.jwt as api_jwt

We can see the `modules.api.jwt` in the import, It mean `modules/api/jwt.py`.

## Routing the handler

The routing is starting in this class:

    @cherrypy.tools.accept(media="application/json")
    class handler(pages.main):

        def __init__(self):
            pages.main.__init__(self)

        def index(self, **kwargs):
            ...
        index.exposed = True

        def <page_name>(self, **kwargs):
            ...
        <page_name>.exposed = True

The `index` on there is `yourdomain.com/`.

let say the `<page_name>` is `about` page. so the route for the `about` is `yourdomain.com/about`.

How about if you want to create a route like this?: `yourdomain.com/about/profile` & `yourdomain.com/about/contact`.

The syntax will like this:

    @cherrypy.tools.accept(media="application/json")
    class handler(pages.main):

        def __init__(self):
            pages.main.__init__(self)

        def index(self, **kwargs):
            kwargs["params_page"] = pages.main().user("home")
            return user_home.main().html(kwargs)
        index.exposed = True

        class about(pages.main):

            def profile(self, **kwargs):
                ...
            profile.exposed = True

            def contact(self, **kwargs):
                ...
            contact.exposed = True

        about=about()

## Add modules into handler

The handler request have a 2 kind method:
- Request with JSON
- Request with POST

### Request with JSON

when you create the handler with JSON request, the pattern will look like this:

    def <page_name>(self, **kwargs):
        <authentication_token_check>
        <session_token>
        <default_response>
        if cherrypy.request.method == 'OPTIONS':
            cherrypy_cors.preflight(allowed_methods=['GET', 'POST'])
        if cherrypy.request.method == 'POST':
            try:
                cherrypy.serving.response.headers['Content-Type'] = 'application/json'
                kwargs["body"]  = cherrypy.request.body.read()
                ...
                <template_parameter>
                <summon_module>
                <response>
                ...
            except Exception as e:
                <error_response>
        return <response>
    <page_name>.exposed = True

### Request with POST

The handler with HTML request pattern look more simple than JSON request one. It look like this:

    def <page_name>(self, **kwargs):
        <authentication_token_check>
        <session_token>
        <template_parameter>
        <summon_module>
        <response>
        return <response>
    <page_name>.exposed = True

You can make it simple the pattern to look like this:

    def <page_name>(self, **kwargs):
        <authentication_token_check>
        <session_token>
        <template_parameter>
        return <summon_module>
    <page_name>.exposed = True

### Explanation

The `<page_name>` is where you name the page.

`<authentication_token_check>` is the optional. It looks like this:

    authentication.token_check(f"{globalvar.GV_base_url}/?message=forbidden")

`<session_token>` is optional. It will use to get the session token and bring it into modules. Here is the example:

    kwargs["session_token"] = cherrypy.session.get("token")

`<default_response>` is the optional one. It is for safely giving return.

When you want to use a template, we can use `<template_parameter>` on there.

For an example we want to use `user` and `email` template, so this is a sample:

    kwargs["template_user"  ] = pages.main().user("register")
    kwargs["template_email" ] = pages.main().email("contact")

The `template_user` and `template_email` now can be use in the module.

`<summon_module>`, `<response>`, and `<error_response>` is the custom things.

You can create a JSON response like this:

    module      = api_jwt.main().change(kwargs)
    response    = json.dumps(module, indent=2)
    return response.encode()

or HTML response like this:

    return page_register.main().html(kwargs)

For the `return`, you can change it into `raise` for redirect. Here is the example:

    ...
    response = api_auth.main().register(kwargs)
    if response["status"] == "success":
        raise cherrypy.HTTPRedirect(f"{globalvar.GV_base_url}/?message=success")
    else:
        raise cherrypy.HTTPRedirect(f"{globalvar.GV_base_url}/?message=failed")
    ...

### Session

To keep session, we can use `cherrypy.session` like this:

    ...
    response = api_auth.main().login(kwargs)
    if response["status"] == "success":
      token     = response["data"]["token"    ]
      username  = response["data"]["username" ]
      try:
        cherrypy.session["token"    ] = str( token    )
        cherrypy.session["username" ] = str( username )
        raise cherrypy.HTTPRedirect('/?message=success')
      except Exception as e:
        print(f"Error: {e}")
        raise cherrypy.HTTPRedirect('/login?message=failed')
    else:
      raise cherrypy.HTTPRedirect('/login?message=failed')
    ...

And for the logout session, you can use this:

    ...
    cherrypy.lib.sessions.expire()
    raise cherrypy.HTTPRedirect(f"{globalvar.GV_base_url}/?message=logout")
    ...

## Sample

### JSON method

    def change(self, **kwargs):
        response = '{}'
        if cherrypy.request.method == 'OPTIONS':
            cherrypy_cors.preflight(allowed_methods=['GET', 'POST'])
        if cherrypy.request.method == 'POST':
            try:
                cherrypy.serving.response.headers['Content-Type'] = 'application/json'
                kwargs["body"]  = cherrypy.request.body.read()
                module          = api_jwt.main().change(kwargs)
                response        = json.dumps(module, indent = 2)
            except Exception as e:
                response = '{}'
        return response.encode()
    change.exposed = True

### POST method

For giving JSON response:

    def change(self, **kwargs):
        module      = api_jwt.main().change(kwargs)
        response    = json.dumps(module, indent=2)
        return response.encode()
    change.exposed = True

For giving HTML response with Template page:

    def register(self, **kwargs):
        kwargs["params_page"] = pages.main().user("register")
        return page_register.main().html(kwargs)
    register.exposed = True

as you can see in here `pages.main().user("register")`, you need to add template pages in the parameter if you want to use a template.

### Combine method

Here is another JSON response that use a Template page:

    def contact(self, **kwargs):
        balikan = '{}'
        if cherrypy.request.method == 'OPTIONS':
            cherrypy_cors.preflight(allowed_methods=['GET', 'POST'])
        if cherrypy.request.method == 'POST':
            try:
                cherrypy.serving.response.headers['Content-Type'] = 'application/json'
                kwargs["body"] = cherrypy.request.body.read()
                kwargs["email"] = pages.main().contact()
                response = api_contact.main().contact(kwargs)
                balikan = json.dumps(response, indent = 2)
            except Exception as e:
                print(f"DEBUG ERROR: {e}")
                balikan = '{}'
        return balikan.encode()
    contact.exposed = True

you can add the Template on the parameter like this:

    kwargs["email"] = pages.main().contact()
