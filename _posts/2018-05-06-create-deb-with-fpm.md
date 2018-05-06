---
layout: post
title: create deb package with fpm
---

```bash
find usr/
usr/
usr/local
usr/local/blackbox
usr/local/blackbox/bin
usr/local/blackbox/bin/blackbox_exporter
usr/local/blackbox/etc
usr/local/blackbox/etc/blackbox.yml
```

`cat debian/preinst`
```bash
#!/bin/sh
set -e

USER=blackbox_exporter
GROUP=$USER

case "$1" in
    install)
        # do some magic
        if ! getent group $GROUP >/dev/null; then
            /usr/sbin/useradd -s /usr/sbin/nologin ${USER}
        fi
        ;;

    upgrade|abort-upgrade)
        ;;

    *)
        if getent group $GROUP >/dev/null; then
            /usr/sbin/userdel -r ${USER}
        fi
        exit 0
        ;;
esac

#DEBHELPER#

exit 0
```

`cat debian/postrm`
```bash
#!/bin/sh
set -e

USER=blackbox_exporter
GROUP=$USER

case "$1" in
    purge|remove)
        # do some magic
        if getent group $GROUP >/dev/null; then
            /usr/sbin/userdel -r ${USER}
        fi
        ;;
    *)
        echo "postrm called with unknown argument \'$1'" >&2
        exit 0
        ;;
esac

#DEBHELPER#

exit 0
```

```bash
#!/bin/bash

set -x

fpm -s dir \
    -t deb \
    -n "blackbox_exporter" \
    -v 0.12.0 \
    --description "prometheus blackbox exporter" \
    --url 'https://github.com/prometheus/blackbox_exporter' \
    --maintainer scloud-yjsop@baidu.com \
    --deb-no-default-config-files \
    --before-install debian/preinst \
    --after-remove debian/postrm \
    --deb-systemd blackbox_exporter.service \
    --deb-use-file-permissions ./usr
```




