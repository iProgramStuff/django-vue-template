# Docker Django Vue Postgres Template ✌️ 🐍

![Vue Logo](/src/assets/logo-vue.png "Vue Logo")
![Django Logo](/src/assets/logo-django.png "Django Logo")

This template is a minimal example for an application using VueJs and Django (RestFramework). Django is pre-configured in this repository to use a Postgres database.

It's setup to have a clear separation: use Vue, Yarn, and Webpack to handle all frontend logic and asset bundling,
and use Django and RestFramework to manage a Data Models, Web API, and serve static files.

While it's possible to add endpoints to serve django-rendered html responses, the intention is to use Django primarily for the backend, and have view rendering and routing and handled by Vue + Vue Router as a Single Page Application (SPA). You can delete the Vue container information in docker-compose.yml file, and simply have Django not serve the vue file if you'd like to have Django serve views or another implementation (but at that point, why would you choose this repo?)

Out of the box, Django will serve the application entry point (`index.html` + bundled assets) at `/` ,
data at `/api/`, and static files at `/static/`. Django admin panel is also available at `/admin/` and can be extended as needed.

The application templates from Vue Cli `create` and Django `createproject` are kept as close as possible to their
original state, except where a different configuration is needed for better integration of the two frameworks.

You should change the username and password of your database in docker-compose.yml (under environment variables for both db and django).

#### Alternatives

If this setup is not what you are looking for, you might want look at other similar projects:

* [ariera/django-vue-template](https://github.com/ariera/django-vue-template)
* [vchaptsev/cookiecutter-django-vue](https://github.com/vchaptsev/cookiecutter-django-vue)

Prefer Flask? Checkout [gtalarico/flask-vuejs-template](https://github.com/gtalarico/flask-vuejs-template)

### Demo
(This is a live demo of the original repo without Docker and Postgres. Todo: fix that)
[Live Demo](https://django-vue-template-demo.herokuapp.com/)

### Includes

* Django
* Django Restframework
* Django Whitenoise, CDN Ready
* Vue Cli 3
* Vue Router
* Docker Compose Ready Configuration
* Postgres Database
* Vuex
* Gunicorn
* Configuration for Heroku Deployment


### Template Structure


| Location             |  Content                                   |
|----------------------|--------------------------------------------|
| `/backend`           | Django Project & Backend Config            |
| `/backend/api`       | Django App (`/api`)                        |
| `/src`               | Vue App .                                  |
| `/src/main.js`       | JS Application Entry Point                 |
| `/public/index.html` | Html Application Entry Point (`/`)         |
| `/public/static`     | Static Assets                              |
| `/dist/`             | Bundled Assets Output (generated at `yarn build` |
| `docker-compose.yml` | Docker Compose Container configuration     |
| `backend_setup.sh`   | Commands run on django container on start  |
| `frontend_setup.sh`  | Commands run on vue container on start     |
| `requirements.txt`   | Required Python packages. Installed by pip on image build   |


## Prerequisites

Before getting started you should have the following installed and running:
- [X] Docker & Docker Compose

Docker should install everything else in their containers.

## Setup Template

```
$ git clone https://github.com/iprogramstuff/django-vue-template
$ cd django-vue-template
```

Setup
```
$ docker-compose up --build
```

## Enter shell of backend containers

```
$ docker exec -it container-name bash
```
Default container names are: docker-django-vue-frontend, docker-django-vue-backend, docker-django-vue-db. You can see them by running `docker container ps`

The Django and Vuejs application will be served at `localhost:8002`.

The dual dev server setup allows you to take advantage of
webpack's development server with hot module replacement.
Proxy config in `vue.config.js` is used to route the requests
back to django's Api on port 8000.


## Static Assets

See `settings.dev` and `vue.config.js` for notes on static assets strategy.

This template implements the approach suggested by Whitenoise Django.
For more details see [WhiteNoise Documentation](http://whitenoise.evans.io/en/stable/django.html)

It uses Django Whitenoise to serve all static files and Vue bundled files at `/static/`.
While it might seem inefficient, the issue is immediately solved by adding a CDN
with Cloudfront or similar.
Use `vue.config.js` > `baseUrl` option to set point all your assets to the CDN,
and then set your CDN's origin back to your domains `/static` url.

Whitenoise will serve static files to your CDN once, but then those assets are cached
and served directly by the CDN.

This allows for an extremely simple setup without the need for a separate static server.

[Cloudfront Setup Wiki](https://github.com/gtalarico/django-vue-template/wiki/Setup-CDN-on-Cloud-Front)

## Deploy

* Set `ALLOWED_HOSTS` on `backend.settings.prod.py`

## THE BELOW DEPLOYMENT STEPS DO NOT WORK WITH THE NEW DOCKER SETUP AND SHOULD BE IGNORED!! I will write them for this version soon.

### Heroku Server

```
$ heroku apps:create django-vue-template-demo
$ heroku git:remote --app django-vue-template-demo
$ heroku buildpacks:add --index 1 heroku/nodejs
$ heroku buildpacks:add --index 2 heroku/python
$ heroku addons:create heroku-postgresql:hobby-dev
$ heroku config:set DJANGO_SETTINGS_MODULE=backend.settings.prod

$ git push heroku
```

Heroku's nodejs buidlpack will handle install for all the dependencies from the `packages.json` file.
It will then trigger the `postinstall` command which calls `yarn build`.
This will create the bundled `dist` folder which will be served by whitenoise.

The python buildpack will detect the `pipfile` and install all the python dependencies.

The `Procfile` will run Django migrations and then launch Django'S app using gunicorn, as recommended by heroku.

##### Heroku One Click Deploy

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/gtalarico/django-vue-template)
