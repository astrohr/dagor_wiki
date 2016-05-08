API can be started in "mock mode", so that it works without any special hardware.

## Install Python env for TCA

##### 1. Create and activate a virtualenv.

Install virtualenvwrapper, for Python **2.7.**
See http://virtualenvwrapper.readthedocs.io/en/latest/install.html for install, config, and basic usage.

A very good alternative to virtualenvwrapper (i.e. virtualenv) is pyenv. It requires somewhat more careful configuration though. https://github.com/yyuu/pyenv

##### 2. Clone the repo

... from GitHub.


##### 3. Install required Python packages

    $ cd path/to/cloned/repo
    $ pip install -r tca/requirements.txt


## Lights API

### Running

To run Lights API in mock mode:

    $ python tca/api/lights.py run --mock

### Using

API is "RESTful". All endpoints support `GET`. Changing data should generally be done with `PUT` though `POST` is also supported, for compatibility.

#### Browsable API

Direct a web browser to http://localhost:5000/lights/. Browsers set `Accept: text/html` HTTP header, which causes FlaskAPI to render a "browsable" (i.e. human readable) version of the API. This version also shows descriptions of endpoints (from docstrings). 


#### Barebones API

When using API "for real", always set `Accept: application/json` HTTP header. This will cause FlaskAPI to render only the minimal resource representation, without any fancy HTML.



