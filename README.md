This repo contains the following artifacts required to run Drupal apps on OpenShift.

1. PHP FPM image running PHP 7.3
2. Nginx image
3. Drupal template

# How to use

## Build the s2i image

```
$ cd s2i-build

$ docker build -t shapeblock/drupal-8:7.3 .

$ docker push shapeblock/drupal-8:7.3

$ oc import-image shapeblock/drupal-8:7.3 --confirm

# or if creating a new tag

$ oc  tag --source=docker shapeblock/drupal-8:7.3 shapeblock/drupal-8:7.3

```

## Build the nginx image

```
$ docker build -t shapeblock/nginx-drupal:1.17 .

$ docker push shapeblock/nginx-drupal:1.17

$ oc import-image shapeblock/nginx-drupal:1.17 --confirm

```

## Upload the template

The template is a quick and UI-friendly way to boot a Drupal 8 application using the above artifacts. This can be imported into the project by running,

```
$ oc apply -f drupal-8-template.yml
```

**NOTE** you have to change the project name in the template according to where you have imported the base images.

