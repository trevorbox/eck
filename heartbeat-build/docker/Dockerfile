FROM docker.elastic.co/beats/heartbeat-ubi8:7.11.0

USER 0

RUN chgrp -R 0 /usr/share/heartbeat && \    
    setcap cap_net_raw=eip /usr/share/heartbeat/heartbeat

USER 1001