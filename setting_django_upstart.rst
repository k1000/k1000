Create the init script for upstart in ``/etc/init/upstart-job.conf`` 
::
  start on runlevel [2345]
  stop on runlevel [06]
  respawn
  respawn limit 10 5
    
  script
    NAME=trenpa
    PORT=8002
    NUM_WORKERS=2
    TIMEOUT=120
    USER=shop
    GROUP=shop
    LOGFILE=/var/log/gunicorn/$NAME.log
    LOGDIR=$(dirname $LOGFILE)
    test -d $LOGDIR || mkdir -p $LOGDIR
    cd /home/shop/archive_db/pro
    exec /home/shop/archive_db/env/bin/gunicorn \
    --env DJANGO_SETTINGS_MODULE=$NAME.settings \
    $NAME.wsgi:application \
    -w $NUM_WORKERS -t $TIMEOUT \
    --user=$USER --group=$GROUP --log-level=debug \
    --name=$NAME -b 78.47.135.2:$PORT
    --log-file=$LOGFILE 2>>$LOGFILE
  end script


Note that the PORT=8002 must match the number you configured in server configuration. In case you are running multiple websites on the same machine you'll have to increment this number accordingly.

Add new system service
::
  # sudo ln -fs /etc/init/upstart-job /etc/init.d/upstart-job

Make it starts at system boot
::
  # sudo update-rc.d upstart-job defaults

And start it now
::
  # sudo service upstart-job start


based on: http://ponytech.net/blog/django-deployement-ubuntu-upstart-nginx-gunicorn-and-virtualenvwrapper
