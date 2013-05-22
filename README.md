# Django-Heroku

This project is intended to be a convenient starting point for building Django projects in Heroku.  It took me quite a while to get over the initial config hurdle of learning enough about both systems to get them working.  (I am a novice in both.)

The main differences between this project and your standard Django starter project are:

- The presence of `requirements.txt` and `Procfile` files.
- A dependence on environmental variables for some aspects of configuration.
- Administration is turned on by default.
- Static files are turned on by default.

An important reference for anyone using Heroku or similar services is [The Twelve-Factor App](http://12factor.net/).  Much of what goes on in setting up your project is centered on items I-VI.

## Requirements

- Python 2.7
- Access to a [PostgreSQL](http://www.postgresql.org/) database, either locally, through Heroku/Amazon, or anywhere else.  In Heroku, the project will use the Heroku Postgres db.
- [Pip](https://pypi.python.org/pypi/pip) for dependency management
- [Heroku Toolbelt](https://toolbelt.heroku.com/)  for Heroku management
- [Git](http://git-scm.com/) version control system.  You should get this for free when you install Heroku.
- [Virtualenv](https://pypi.python.org/pypi/virtualenv) is optional, but very useful.  These instructions don't use it, but if you work in several different Python environments then it's a useful tool.

Once you have all of the pieces you can begin.

## Running the Project Locally

### Copy Files

Copy the files to whatever folder you want.  You can fork and clone the repo, or download an archive of the project as a zip file from GitHub.

### Install Dependencies

From the project directory install the Python libraries you need with `sudo pip install -r requirements.txt`.  This will download and install requirements you don't already have.

### Create Environment

Some of the key configurations actually live in the environment variables.  The two variables you have to configure are the full path to the static file directory (which is the project's `static` directory), and the url of your Postgres instance.

Create a `.env` file, which defines the environment for your application.  We will always run the application through Foreman, which will read the variables from `.env` before running your project.

Here is a sample env file:

    DATABASE_URL=postgres://user:pass@localhost:5432/my_db
    DJ_STATIC_DIR=/your/project/dir/static

### Sync and Collect

Now run collectstatic and syncdb, using Foreman.  Foreman was included with your Heroku install:

    foreman run python manage.py syncdb
    foreman run python manage.py collectstatic

### Run the Server

Now run the server.  

    foreman start
    
You should be able to access your application on [http://127.0.0.1:5000/admin/](http://127.0.0.1:5000/admin/).

## Running the Project in Heroku

If your project isn't already in Git, now is a good time to initialize your repo.

    git init

Create the Heroku app.  As a side-effect this will add a remote named "heroku" to your git repo.  Then configure your static directory path.

    heroku apps:create [appname]
    heroku config:set DJ_STATIC_DIR=/app/static
    
Provision a dev database, too.  This will automatically set the DATABASE_URL variable.

    heroku addons:add heroku-postgresql:dev

Now you can push your repo, and watch all the lines of output.  Then run syncdb through heroku.

    git push heroku master
    heroku run python manage.py syncdb

Finally, you can provision a web worker and try opening your app in a browser.

    heroku ps:scale web=1
    heroku apps:open


## Maintaining the Project

As you add new dependencies, remember to update your `requirements.txt` file.

Any configuration differences between your development environment and your staging/production environment need to be separated out into environment-based configurations.  A Heroku application is, at its heart, a combination of your slug plus the config environment.
