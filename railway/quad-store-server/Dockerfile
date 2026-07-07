FROM atomgraph/fuseki:4.6

COPY cluster.trig /tmp/mount/cluster.trig

CMD ["--file=/tmp/mount/cluster.trig", "--update", "/ds"]
