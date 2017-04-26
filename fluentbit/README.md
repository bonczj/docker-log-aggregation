# Fluentbit Configuration
The following Fluentbit configuration will be installed as part of the
deployment process.

```
[SERVICE]
    Flush        5        # Process new messages in 5 second chucks
    Daemon       On       # Run fluentbit as a background daemon
    Log_Level    debug    # Process debug and higher log entries

[INPUT]
    Name        forward
    Listen      0.0.0.0   # Ensure Fluentbit listens on all interfaces
    Port        24224     # Default fluentd port. Fork if you need to change
    Buffer_Size 4096      # Allow holding up to 4MB of data at once
    Chunk_Size  32        # (default) Allocate buffer pages in 32KB chuncks

[OUTPUT]
    Name            es
    Match           *
    Host            0.0.0.0   # Elasticsearch will run and bind ports to the host
    Port            29200     # A non-standard port on the host to avoid conflicts
    Index           fluentbit # Required
    Type            docker    # Required
    Logstash_Format On        # Enable Logstash data format in Elasticsearch
    Logstash_Prefix logstash  # (default) Use logstash-<date> for the index
```

The Logstash_Format is enabled in Elasticsearch. This allows the log data
to be stored in an index based on the current date. Using separate indexes
allows for better performance overall of Elasticsearch. It also provides
a way to more easily purge old data if it is needed by dropping the
indexes based on date.

If there is a conflict on the fluentd port (24224), you should move the 
conflicting service to another port. It is easier to let fluentd run on
its standard port.

If there is a conflict on the Elasticsearch port (29200), you can resolve
this by forking the code and changing both the fluentbit configuration
as well as the port mapping in the docker-compose.yml file. You can also
change both of these files after installation, but you would need to 
remember to do this on each new host that potentially has those conflicts.