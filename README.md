# siomiz/blackhole

`ip route add blackhole` any IP address that failed SSH login.

* `/var/log/journal` is the default location of journal files on CoreOS; change as neccesary.
* `--net=host --privileged` is required for iptables from inside container to work

## systemd Service file example

    [Unit]
    Description=Blackhole
    After=docker.service
    Requires=docker.service

    [Service]
    TimeoutStartSec=0
    ExecStartPre=-/usr/bin/docker kill blackhole
    ExecStartPre=-/usr/bin/docker rm blackhole
    ExecStartPre=/usr/bin/docker pull siomiz/blackhole
    ExecStart=\
    /usr/bin/docker run \
      --name blackhole \
      --net=host \
      --privileged \
      -v /var/log/journal:/var/log/journal:ro \
      -v /usr/bin/journalctl:/usr/bin/journalctl \
      -v /lib64:/usr/local/lib64:ro \
      siomiz/blackhole
    
    [Install]
    WantedBy=multi-user.target