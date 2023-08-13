# CostaPy Modules - Main Process

## Giving the response

Main response have 3 options:
- status
- desc
- data

The response status only have 2 options:
- success
- failed

Example for the success response:

    response["status"   ] = "success"
    response["desc"     ] = "Product list collected"
    response["data"     ] = {
        "product"   : product_list,
        "other"     : "Some random text"
    }

Example for the failed response:

    response["status"   ] = "failed"
    response["desc"     ] = "Something went wrong"
    response["data"     ] = {
        "exception"   : str(e)
    }

## Fetching data

### fetchone

    cursor.execute(f"SELECT * FROM tablename WHERE fieldname = {id} ")
    row = cursor.fetchone()

    token   = row['token'    ].decode()
    id      = row['id'       ]
    name    = row['name'     ]
    email   = row['email'    ]

### fetchall

    cursor.execute(f"SELECT * FROM product ")
    product_list = cursor.fetchall()

### Nested fetchall

Variables:
- `l1` is mean `List` level `1`
- `c2` is mean `Count` level `2`
- `d3` is mean `Data` level `3`
- etc

Sample:

    texture_list = []

    cursor.execute(f"SELECT * FROM tableone WHERE fieldname = '{key}' ")
    l1 = cursor.fetchall()
    c1 = 0
    for d1 in l1:

        texture_list.append({
            "id"    :d1["id"    ],
            "name"  :d1["name"  ],
            "desc"  :d1["desc"  ]
        })

        cursor.execute(f"SELECT * FROM tabletwo WHERE keyfield = '{d1['id']}' ")
        l2 = cursor.fetchone()
        texture_list[c1]["owner"] = l2

        cursor.execute(f"SELECT * FROM tablethree WHERE keyfield = '{d1['id']}' ")
        l2 = cursor.fetchall()
        texture_list[c1]["file"] = []
        c2 = 0
        for d2 in l2:
            texture_list[c1]["file"].append({
                "id"        :d2["id"        ],
                "filedir"   :d2["filedir"   ],
                "filetype"  :d2["filetype"  ]
            })

            c2 += 1

        c1 += 1

## Get the last row ID from insert query

    cursor.execute(f"INSERT INTO `product_files` VALUES (DEFAULT, '{webdir}', '{filename}' ) ")
    product_files_lastrowid = cursor.lastrowid

## Begin, Rollback, and Commit

Begin, rollback, and commit can be useful if you use more than 1 process that cannot be separate. For example: more than 1 table insertion query, inserting query while upload success, etc.

The pattern:

    cursor.execute("BEGIN;")
    try:
        # Process and response
    except Exception as e:
        cursor.execute("ROLLBACK;")
        # Process and response when failed
    cursor.execute("COMMIT;")

Sample:

    cursor.execute("BEGIN;")

    try:

        cursor.execute(f"INSERT INTO `files` VALUES (DEFAULT, '{webdir}', '{filename}' ) ")
        files_lastrowid = cursor.lastrowid

        cursor.execute(f"INSERT INTO `thumbnail` VALUES (DEFAULT, '{image}', '{files_lastrowid}' ) ")

        response["status"   ] = "success"
        response["desc"     ] = "insert success"

        loggorilla.prcss(APIADDR, f"insert success")

    except Exception as e:

        cursor.execute("ROLLBACK;")

        response["status"   ] = "failed"
        response["desc"     ] = "There is error when processing try. See the exception for the clue."
        response["data"     ] = {
            "exception" : str(e)
        }

        loggorilla.error(APIADDR, f"{str(e)}")

    cursor.execute("COMMIT;")

## File management

### Uploading

    mediafile   = params["mediafile"]
    name        = "helloworld"
    ext         = pathlib.Path(mediafile.filename).suffix
    dir         = f"/srv/media/material/texture"

    uploading.main(mediafile, name+ext, dir)

It will be overwrite if the file already on there.

### Removing

    import pathlib
    import glob
    import os

    dir     = pathlib.Path(f"/srv/media/material/texture")
    name    = "helloworld.png"

    for row in glob.iglob(os.path.join(dir, name)):
        os.remove(row)

It can combining with `*`, for example:

    dir     = pathlib.Path(f"/srv/media/product/*/file")
    name    = "*.zip"
