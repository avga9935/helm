## Introduction
We are going to setup and deploy Django image using Dockerfile.

# NOTE: DO NOT PROCEED UNTIL DOCKERFILE IS CREATED AND PUSHED INTO THE DOCKERHUB (while running the dockerfile using docker run command container may not start check the logs if logs have issues with the SECRET_KEY then its okay)

## STEP 1 : Creating Dockerfile for django
```
bash
mkdir django
cd django
nano Dockerfile
```
```
FROM python:3.7.4-alpine3.10

ADD django-polls /app

WORKDIR /app

RUN set -ex \
    && apk add --no-cache --virtual .build-deps postgresql-dev build-base \
    && python -m venv /env \
    && /env/bin/pip install --upgrade pip \
    && /env/bin/pip install --no-cache-dir -r /app/requirements.txt \
    && runDeps="$(scanelf --needed --nobanner --recursive /env \
        | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
        | sort -u \
        | xargs -r apk info --installed \
        | sort -u)" \
    && apk add --virtual rundeps $runDeps \
    && apk del .build-deps

ENV VIRTUAL_ENV /env
ENV PATH /env/bin:$PATH

EXPOSE 8000

CMD ["gunicorn", "--bind", ":8000", "--workers", "3", "mysite.wsgi"]
```

Now add the django repo which will hel us to run django image with all configurations in it and then add a file in it
```
bash
git clone --single-branch --branch polls-docker https://github.com/do-community/django-polls.git
cd django-polls
nano requirements.txt
```
```
# requirments.txt

boto3==1.9.252
botocore==1.12.252
Django==2.2.6
django-storages==1.7.2
docutils==0.15.2
gunicorn==19.9.0
jmespath==0.9.4
psycopg2==2.8.3
python-dateutil==2.8.0
pytz==2019.3
s3transfer==0.2.1
six==1.12.0
sqlparse==0.3.0
urllib3==1.25.6
```

Now edit settings.py file under django-polls/mysite/settings.py
```
# first 3 lines
import os
import json
import logging.config

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = os.getenv('DJANGO_SECRET_KEY')

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = os.getenv('DJANGO_DEBUG', False)

ALLOWED_HOSTS = os.getenv('DJANGO_ALLOWED_HOSTS', '127.0.0.1').split(',')

# Scroll down and look for database section and add

# Database
# https://docs.djangoproject.com/en/2.1/ref/settings/#databases

DATABASES = {
     'default': {
         'ENGINE': 'django.db.backends.{}'.format(
             os.getenv('DATABASE_ENGINE', 'sqlite3')
         ),
         'NAME': os.getenv('DATABASE_NAME', 'polls'),
         'USER': os.getenv('DATABASE_USERNAME', 'myprojectuser'),
         'PASSWORD': os.getenv('DATABASE_PASSWORD', 'password'),
         'HOST': os.getenv('DATABASE_HOST', '127.0.0.1'),
         'PORT': os.getenv('DATABASE_PORT', 5432),
         'OPTIONS': json.loads(
             os.getenv('DATABASE_OPTIONS', '{}')
         ),
     }
 }

# Now go to the bottom and add

# Logging Configuration

# Clear prev config
LOGGING_CONFIG = None

# Get loglevel from env
LOGLEVEL = os.getenv('DJANGO_LOGLEVEL', 'info').upper()

logging.config.dictConfig({
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'console': {
            'format': '%(asctime)s %(levelname)s [%(name)s:%(lineno)s] %(module)s %(process)d %(thread)d %(message)s',
        },
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'console',
        },
    },
    'loggers': {
        '': {
            'level': LOGLEVEL,
            'handlers': ['console',],
        },
    },
})
```
Now dockerfile is ready to go now just build, run and push this image to the dockerhub repo
```
docker build -t avga9935/django:latest .
docker run -d --name dj avga9935/django:latest
docker ps -a
docker logs dj
# use this command to check container status if the issue is related with STATIC_KEY then we can proceed otherwise troubleshoot accordingly
docker push avga9935/django:latest
```
