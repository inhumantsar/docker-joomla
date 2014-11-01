docker-joomla
=============

Builds out a Joomla container running the latest from their master branch inside Apache/PHP.

## Run Script ##

The following should should put up a functional Joomla install ready to be configured.

*Note:* If you want your Joomla install to survive a docker rm, add `-v /path/on/host/configuration.php:/app/configuration.php \` on a new line below `-p $HOST_PORT:80 \` to the Joomla docker run command and `-v /path/on/host/mysql:/var/lib/mysql \` on a new line below `-e MYSQL_DATABASE="$DB" \` in the MySQL docker run command.

    #!/bin/bash
    HOST_PORT=80
    CT_DB='joomla-mysql'
    CT_WWW='joomla'
    DB_ROOT_PASS='moo'
    DB_USER='joomla'
    DB_PASS='mootoo'
    DB='joomla'

    if ! [[ -z `docker ps -a | grep $CT_DB` ]]; then
      if [[ -z `docker ps | grep $CT_DB` ]]; then docker start $CT_DB; fi
    else
      docker run  -d --name "$CT_DB" \
                  -e MYSQL_ROOT_PASSWORD="$DB_ROOT_PASS" \
                  -e MYSQL_USER="$DB_USER" \
                  -e MYSQL_PASSWORD="$DB_PASS" \
                  -e MYSQL_DATABASE="$DB" \
                  centurylink/mysql
    fi

    if ! [[ -z `docker ps -a | grep $CT_WWW` ]]; then
      if [[ -z `docker ps | grep $CT_WWW` ]]; then docker start $CT_WWW; fi
    else
      docker pull inhumantsar/docker-joomla
      docker run  -d --name="$CT_WWW" \
                  --link $CT_DB:mysql \
                  -p $HOST_PORT:80 \
                  inhumantsar/docker-joomla
