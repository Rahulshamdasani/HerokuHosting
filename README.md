# HerokuHosting
Notes for avoiding errors while hosting


## Backend
<ol>
  <li>While hosting the database should only be <strong style = "color:magenta">postgres no sqlite</strong>
        <ul>
        <li> if database is sqlite, follow these steps.</li>
        <li> <a href = "https://pypi.org/project/dj-database-url/">Reference for dj-database-url </a> </li>
        <li> install this library <code>pip install dj-database-url</code></li>
        <li> in the main settings.py you will have a section where your sqlite database is declared, below that write these commands</li>
        <li> <code> import dj_database_url <br/>
                    db_from_env = dj_database_url.config(conn_max_age=600) <br/>
                    DATABASES['default'].update(db_from_env) <br/>
            </code>
        </li>
        </ul>
    <li> install two extra packages
        <ul>
            <li> gunicorn <a href = "https://pypi.org/project/gunicorn/"> Documentation </a> 
                <ul>
                    <li> install it with command <code> pip install gunicorn </code></li>
                    <li> Nothing else needs to be done, it is used by heroku for deploying website</li>
                </ul>
            </li>
            <li>whitenoise <a href = "https://whitenoise.evans.io/en/stable/"> Documentation </a> 
            <ul>
                    <li> install it with command <code> pip install whitenoise </code></li>
                    <li> Put it inside middleware in settings.py</li>
                    <li> Make sure it is below SecurityMiddleware</li>
                    <code>MIDDLEWARE = [<br/>
                            # 'django.middleware.security.SecurityMiddleware', <br/>
                            'whitenoise.middleware.WhiteNoiseMiddleware', <br/>
                            # ... <br/>
                            ]</code>
                            </li>
                </ul>
            </li>  
        </ul>
    </li>
    <li>
        Install django heroku<br/>
        at the start of page write this command<br/>
        <code>import django_heroku</code><br/>
        At the end of page wriite this command<br/>
        <code>django_heroku.settings(locals())</code>
    </li>
    <li> Make a repository on git and it should be private.</li>
    <li> Add a requirements.txt file. It will consist of all the dependencies for your project.<br/>
        create it with this command<code>
            pip freeze > requirements.txt
        </code>
    <li>Create a proc file<br/>
        its contents should be like<br/>
        <code><br/>
            release: python manage.py makemigrations--no-input<br/>
            release: python manage.py migrate --no-input<br/>
            web: gunicorn backendAPI.wsgi <br/>
        </code>
        what this says is:
        <ul>
            <li> <span style = "color:magenta">third line</span> specifies the location of wsgi file.
            <li> In my case it is inside backendAPI folder, so the name is ParentFolder.wsgi.
            <li> Change this command based on whereever you have kept your wsgi file.
            <li> by default it is with settings.py file.
            <li> <span style = "color:magenta">first</span> line says that, once the app is install, look for required migrations
            <li> <span style = "color:magenta">Second</span> line says, to make the required migrations.
        </ul>
    <li> <span style = "color:magenta">Errors I ran into</span>
        <ul>
            <li> collecstatic is not set to 1 while compiling app inside heroku logs:<br/>
            go to heroku Dashboard -> App -> settings -> display env variables -> set key as COLLECT_STATIC and value as 1.
            <li> Make sure you have django-heroku as mentioned in step 3 and all the settings in settings.py file
            <li> Cors error.
                <ul>
                    <li> request is denied because of cors policy.
                    <li> install django cors header <a href = "https://pypi.org/project/django-cors-headers/">Reference Documentation</a> 
                    <li> command <code> pip install django-cors-headers</code>
                    <li> Insert in Installed apps:<br/>
                    <code>"corsheaders",</code>
                    <li> Insert in middleware, order of this matters, I struggled for three days because of this order so refer the settings.py file in this repo for getting reference.<br/>
                    mostly it will be after whitenoise, security and sessions<br/>
                    <span style="color:magenta"> It should definitely be before whitenoise</span>
                    <code>"corsheaders.middleware.CorsMiddleware",<br/>
    "django.middleware.common.CommonMiddleware",</code>
                    <li><pre> Add your allowed hosts:
                    CORS_ALLOWED_ORIGINS = [
                        "https://example.com",  # frontend hosted
                        "https://sub.example.com",  # Backend hosted
                        "http://localhost:8080",
                        "http://127.0.0.1:9000",
                    ]
                    </pre>
                </ul>
            </li>
        </ul>
    </li>
    <li> 
        <span style="color:magenta"> Cors header extension</span><br/> 
        if all the above stated solutions doesnt work
        <ul>
            <li> At the end of setting.py file add <br/>
            <code>CORS_ORIGIN_WHITELIST = 'koobecaffrontend.herokuapp.com',</code>
            <li> add this<br/>
                <code>WSGI_APPLICATION = 'backendAPI.wsgi.application'<br/>
                    CORS_ALLOWED_ORIGINS = [   <br/>
                        '*',<br/>
                        'https://social-media-frontend-two.vercel.app', <br/>
                    ]
                </code>
            </li>
            <li> if still its not working:<br/>
                <code> CORS_ALLOW_ALL_ORIGINS = True</code>
      
        
