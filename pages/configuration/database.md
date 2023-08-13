# Database (config/database.py)

This is the sample template for configure it

    db_default = {
        'host'          : 'localhost',
        'user'          : 'root',
        'password'      : '',
        'database'      : 'your_db',
        'autocommit'    : True,
    }

You also can make more than 1 database configuration like this

    db_default = {
        'host'          : 'localhost',
        'user'          : 'root',
        'password'      : '',
        'database'      : 'your_db',
        'autocommit'    : True,
    }

    db_other = {
        'host'          : 'localhost',
        'user'          : 'root',
        'password'      : '',
        'database'      : 'other_db',
        'autocommit'    : True,
    }
