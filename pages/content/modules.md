# CostaPy Modules

## 1. Create APIADDR variable for LogGorilla

    APIADDR = "/your/api/page/directory"

## 2. Declare default response

    response = {}

## 3. Connect database

    main_db = mariadb.connect(**database.main_db)
    cursor  = main_db.cursor(dictionary=True)

## 4. Declare parameters

For POST method

    jwt_token   = params["jwt"          ]

    id          = params["id"           ]
    name        = params["name"         ]
    email       = params["email"        ]

For JSON method

    body        = params["body"].decode()
    form_param  = json.loads(body)

    jwt_token   = form_param["jwt"      ]

    id          = form_param["id"       ]
    name        = form_param["name"     ]
    email       = form_param["email"    ]

## 5. Get payload from JWT

    public_key  = open('.ssh/id_rsa.pub', 'r').read()
    key         = serialization.load_ssh_public_key(public_key.encode())
    header_data = jwt.get_unverified_header(jwt_token)
    payload     = jwt.decode(
        jwt         = jwt_token,
        key         = key,
        algorithms  = [header_data['alg'], ]
    )

    session_id  = payload["session_id"]

## 6. Main progress (try/except)

See `main-process.md` for the detail.

## 7. Close database

    main_db.close()

## 8. Return response

    return response
