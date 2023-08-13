# Templating (config/template.py)

Templating is useful when you had more than 1 website template for difference use case. For an example, when you had user and admin in the use case actor, let say we can do the website for user have a navbar and footer, and the website for admin have a navbar and sidebar.

Before you create a template, make sure your `directory` configuration is ready for storing templates and pages. For an example:

    user_page       = "static/user/pages"
    user_template   = "static/user/template"

To create the template, you need to insert this code in `def __init__(self)`

    self.html_user_pages        = html.main.get_html(directory.user_page)
    self.html_user_template     = html.main.get_html(directory.user_template)

if you had admin template or email template, you just need to add the code. for the example like this

    self.html_user_pages        = html.main.get_html(directory.user_page)
    self.html_user_template     = html.main.get_html(directory.user_template)

    self.html_admin_pages       = html.main.get_html(directory.admin_page)
    self.html_admin_template    = html.main.get_html(directory.admin_template)

    self.html_email_pages       = html.main.get_html(directory.email_page)
    self.html_email_template    = html.main.get_html(directory.email_template)

and then you need create function for each of your template in main class like this

    def user(self, page):
        params_list = {
            "template"  : self.html_user_template        ["user.html"           ],
            "topnav"    : self.html_user_template        ["user-topnav.html"    ],
            "container" : self.html_user_pages           [page+".html"          ]
        }
        return params_list
