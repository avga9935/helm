## Introduction
We are going to setup and deploy Django app using PostreSQL and Django, after creating a Django image using Dockerfile.

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
