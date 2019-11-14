# [Artificially-Intelligent/shiny](https://github.com/Artificially-Intelligent/shiny)

## Description
rocker/rbase docker image with a selection of packages preinstalled geared to support R-Shiny based webapps. Also come with option to install additional packages at cotainer startup for packages refrenced by a library('package') statment within any *.R file copied/mounted into container.  

## Usage

Here are some example snippets to help you get started creating a container.

## docker

docker create \
  --name=myshinyapp \
  -p 8080:8080 \
  -e DISCOVER_PACKAGES=true \
  -v path/to/data/source:/01_input \
  -v path to code:/02_code \
  -v path/to/data/output:/04_output \
  --restart unless-stopped \
  slink42/rbase_shiny

## docker-compose

Compatible with docker-compose v2 schemas.

---
  version: "2"
  services:
    shiny:
      image: slink42/rbase_shiny
      container_name: myshinyapp
      environment:
        - DISCOVER_PACKAGES=true
        - PORT=4848
        - SHINYCODE_GITHUB_REPO=https://github.com/rstudio/shiny-examples
      volumes:
        - path/to/data/source:/01_input
        - path to code:/02_code
        - path/to/data/output:/04_output
      ports:
        - 4848:4848
      restart: unless-stopped

## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 3838:8080` | Specify a port mapping from container to host for shiny server web ui. Port value after the : should match that defined by PORT environment variable or the default value 8080 |
| `-e PORT=8080` | Specify a port for shiny to use inside the container. Included to support deployment to google cloud run. If not set default value is 8080 |
| `-e SHINYCODE_GITHUB_REPO=https://github.com/rstudio/shiny-examples` | Specifiy a url for a github repo to copy to code directory at container runtime. Note only supports https, not ssh. Private repo can be added by including an access token in the url eg. https://myaccesstoken@github.com/mygithubuser/mygithubrepo.git | 
| `-e PUID=shiny` | Specify the user to run shiny server as. Defaults to shiny if not supplied |
| `-e DISCOVER_PACKAGES=true` | Set true to have  *.R files in /code & /02_code directories + subdirectories scanned for library(package) entries. Missing package will be installed as part of contrianer startup. |
| `-v /01_input` | Placeholder folder for source data mapping. R-Shiny apps can map to this location using ../01_input |
| `-v /02_code` | The web root for shiny. R shiny code reside here. |
| `-v /04_output` | Placeholder folder for output data storage. R-Shiny apps can map to this location using ../04_output |
| `-v /05_logs` | Placeholder folder for log file output. R-Shiny apps can map to this location using ../05_logs |


## Preinstalled Packages

See [default_install_packages.csv](https://github.com/Artificially-Intelligent/shiny/blob/master/default_install_packages.csv) for full list of packages currently included by default.

## Deploying to Google Cloud Run
Look at instructions here for the general process of how to:
  Deloy container image to Google Container Registry: https://cloud.google.com/run/docs/deploying
  Deloy Container Registry image as a Google Cloud Run service: https://cloud.google.com/run/docs/deploying

## Troubleshooting

Run package, start shiny-server and view logs
  docker run -it -p 3838:3838 -e PORT=3838 --name shiny artificiallyintelligent/shiny:latest /bin/bash
  setsid /usr/bin/shiny-server.sh >/dev/null 2>&1 < /dev/null &
  cat /var/log/shiny-server/code-shiny-*

Check if there is sufficent disc space available for temp files
  df -h /tmp