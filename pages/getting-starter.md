# Getting Starter

## Requirement

You need a Python and this libraries to use CostaPy:
- cherrypy
- cherrypy-cors
- mako
- mysql-connector
- bcrypt
- pyjwt[crypto]

## Download

Download from repository

    git clone https://github.com/ditaAjiPratama/costapy

## Installation

You can install it with run this command

    sh install.sh

Here is the completed command

    sudo apt-get install -y python3-pip
    pip install --upgrade pip
    pip install cherrypy
    pip install cherrypy-cors
    pip install mako
    pip install mysql-connector
    pip install bcrypt
    pip install pyjwt[crypto]

## Usage

Use this command to start the web service

    python<ver> costa.py <ip_address> <port> <service_name>

For an example like this

    python3 costa.py localhost 80 My_Service

You can use nohup too and running it in the background like this

    nohup python3 costa.py localhost 80 My_Service &
