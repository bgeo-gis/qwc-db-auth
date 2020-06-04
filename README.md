[![](https://github.com/qwc-services/qwc-db-auth/workflows/build/badge.svg)](https://github.com/qwc-services/qwc-db-auth/actions)
[![](https://img.shields.io/docker/pulls/sourcepole/qwc-db-auth)](https://hub.docker.com/r/sourcepole/qwc-db-auth)

Authentication with User DB
===========================

Authentication service with local user database.


Configuration
-------------

The static config files are stored as JSON files in `$CONFIG_PATH` with subdirectories for each tenant,
e.g. `$CONFIG_PATH/default/*.json`. The default tenant name is `default`.

### Admin Gui Service config

* [JSON schema](schemas/qwc-db-auth.json)
* File location: `$CONFIG_PATH/<tenant>/dbAuthConfig.json`

Example:
```json
{
  "$schema": "https://raw.githubusercontent.com/qwc-services/qwc-db-auth/master/schemas/qwc-db-auth.json",
  "service": "db-auth",
  "config": {
    "db_url": "postgresql:///?service=qwc_configdb"
  }
}
```

Set the `MAX_LOGIN_ATTEMPTS` environment variable to set the maximum number of
failed login attempts before sign in is blocked (default: `20`).

Besides the form based DB login, an (insecure) plain POST login is supported. This method can be
activated by setting `POST_PARAM_LOGIN=True`. User and password are passed as POST parameters
`username` and `password`.
Usage example: `curl -d 'username=demo&password=demo' http://localhost:5017/login`.

[Flask-Mail](https://pythonhosted.org/Flask-Mail/) is used for sending mails like password resets. These are the available options:
* `MAIL_SERVER`: default ‘localhost’
* `MAIL_PORT`: default 25
* `MAIL_USE_TLS`: default False
* `MAIL_USE_SSL`: default False
* `MAIL_DEBUG`: default app.debug
* `MAIL_USERNAME`: default None
* `MAIL_PASSWORD`: default None
* `MAIL_DEFAULT_SENDER`: default None
* `MAIL_MAX_EMAILS`: default None
* `MAIL_SUPPRESS_SEND`: default app.testing
* `MAIL_ASCII_ATTACHMENTS`: default False

In addition the standard Flask `TESTING` configuration option is used by Flask-Mail in unit tests.

### Two factor authentication

Two factor authentication using TOTP can be enabled by setting the environment variable `TOTP_ENABLED=True`.
This will require an additional verification token after sign in, based on the user's TOTP secret.

A personal QR code for setting up the two factor authentication is shown to the user on first sign in (or if the TOTP secret is empty).
The TOTP issuer name for your application can be set using the environment variable `TOTP_ISSUER_NAME="QWC Services"`.

An user's TOTP secret can be reset by clearing it in the Admin GUI user form.


Usage
-----

Run standalone application:

    python server.py

Endpoints:

    http://localhost:5017/login

    http://localhost:5017/logout


Development
-----------

Create a virtual environment:

    virtualenv --python=/usr/bin/python3 .venv

Activate virtual environment:

    source .venv/bin/activate

Install requirements:

    pip install -r requirements.txt

Set the `CONFIG_PATH` environment variable to the path containing the service config and permission files when starting this service (default: `config`).

    export CONFIG_PATH=../qwc-docker/demo-config

Configure environment:

    echo FLASK_ENV=development >.flaskenv
    export MAIL_SUPPRESS_SEND=True
    export MAIL_DEFAULT_SENDER=from@example.com

Start local service:

     python server.py
