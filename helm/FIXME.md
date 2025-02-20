- [Native Kubernetes](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/deployment/resource-providers/native_kubernetes/)
- [Kubernetes Configuration](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/deployment/config/#kubernetes)
- test
  - flink가 실행되는 환경에 HADOOP_CONF_DIR이 설정되어야 한다.
  - hive.metastore.uris 설정은 HADOOP CONF(core-site.xml)에 포함되어야 한다.
  - session cluster
    ```
    
    ./bin/kubernetes-session.sh -Dkubernetes.cluster-id=localtest-session-cluster -Dkubernetes.service-account=flink -Dkubernetes.namespace=flinkcdc -Dkubernetes.artifacts.local-upload-enabled=true  -Dkubernetes.hadoop.conf.config-map.name=flinkcdc-hive-site-cm -Dkubernetes.container.image.ref=registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle -Dkubernetes.pod-template-file.default=/Users/noname/Workspace/src/github/streaming/flinkcdc-oracle/doc/job/pod-template.yaml -Dkubernetes.rest-service.exposed.type=NodePort -Dkubernetes.artifacts.local-upload-target=s3a://tlake-ns2/jars
    
    JobGraph 생성 이전의 코드는 flink에서 실행한다.
    core-site에 metastore.uris가 설정되어야 하고, HADOOP_CONF_DIR 환경변수 설정 필요
    - job submit
    ./bin/flink run \
        --target kubernetes-session \
        -Dkubernetes.cluster-id=localtest-session-cluster /Users/noname/Workspace/src/github/streaming/flinkcdc-oracle/target/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar  --database.hostname 10.10.27.21 --database.port "1521" --database.username flinkcdc --database.password flinkcdc --database.dbname ORCLCDB --database.schema SOE --database.table TEST_NUMBER_TABLE --sink.metastore.uri thrift://10.10.27.26:32010 --sink.warehouse s3a://tlake-ns2/warehouse --write.parallelism "1" --debezium.mining.strategy online_catalog
    
    ./bin/flink run \
        --target kubernetes-session \
        -Dkubernetes.cluster-id=localtest-session-cluster local:///opt/flink/usrlib/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar  --database.hostname 10.10.27.21 --database.port "1521" --database.username flinkcdc --database.password flinkcdc --database.dbname ORCLCDB --database.schema SOE --database.table TEST_NUMBER_TABLE --sink.metastore.uri thrift://10.10.27.26:32010 --sink.warehouse s3a://tlake-ns2/warehouse --write.parallelism "1" --debezium.mining.strategy online_catalog
    ```
    
    - FIXME: iceberg-flink 를 인식하지 못한다...
    ```
    Enabling required built-in plugins
    Linking flink-s3-fs-hadoop-1.20.0.jar to plugin directory
    Successfully enabled flink-s3-fs-hadoop-1.20.0.jar
    /opt/flink/bin/config-parser-utils.sh: line 45: /opt/flink/conf/config.yaml: Read-only file system
    Starting kubernetes-taskmanager as a console application on host localtest-session-cluster-taskmanager-1-1.
    WARNING: Unknown module: jdk.compiler specified to --add-exports
    WARNING: Unknown module: jdk.compiler specified to --add-exports
    WARNING: Unknown module: jdk.compiler specified to --add-exports
    WARNING: Unknown module: jdk.compiler specified to --add-exports
    WARNING: Unknown module: jdk.compiler specified to --add-exports
    2025-02-20 08:18:28,906 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.jvm-overhead.min=201326592b
    2025-02-20 08:18:28,906 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,906 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.framework.off-heap.size=134217728b
    2025-02-20 08:18:28,906 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,906 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.network.max=134217730b
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.framework.heap.size=134217728b
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.managed.size=536870920b
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.task.heap.size=402653174b
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.numberOfTaskSlots=4
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     -D
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -     taskmanager.memory.jvm-overhead.max=201326592b
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] -  Classpath: /opt/flink/lib/aws-java-sdk-bundle-1.11.901.jar:/opt/flink/lib/commons-configuration2-2.1.1.jar:/opt/flink/lib/commons-logging-1.1.3.jar:/opt/flink/lib/flink-cep-1.20.0.jar:/opt/flink/lib/flink-connector-files-1.20.0.jar:/opt/flink/lib/flink-csv-1.20.0.jar:/opt/flink/lib/flink-json-1.20.0.jar:/opt/flink/lib/flink-scala_2.12-1.20.0.jar:/opt/flink/lib/flink-sql-connector-hive-3.1.3_2.12-1.20.0.jar:/opt/flink/lib/flink-sql-connector-oracle-cdc-3.2.1.jar:/opt/flink/lib/flink-table-api-java-uber-1.20.0.jar:/opt/flink/lib/flink-table-planner-loader-1.20.0.jar:/opt/flink/lib/flink-table-runtime-1.20.0.jar:/opt/flink/lib/guava-27.0-jre.jar:/opt/flink/lib/hadoop-auth-3.2.4.jar:/opt/flink/lib/hadoop-aws-3.2.4.jar:/opt/flink/lib/hadoop-common-3.2.4.jar:/opt/flink/lib/hadoop-hdfs-client-3.2.4.jar:/opt/flink/lib/hadoop-mapreduce-client-core-3.2.4.jar:/opt/flink/lib/htrace-core4-4.1.0-incubating.jar:/opt/flink/lib/iceberg-flink-runtime-1.7.1.jar:/opt/flink/lib/log4j-1.2-api-2.17.1.jar:/opt/flink/lib/log4j-api-2.17.1.jar:/opt/flink/lib/log4j-core-2.17.1.jar:/opt/flink/lib/log4j-slf4j-impl-2.17.1.jar:/opt/flink/lib/ojdbc8-19.3.0.0.jar:/opt/flink/lib/stax2-api-4.2.1.jar:/opt/flink/lib/woodstox-core-5.3.0.jar:/opt/flink/lib/flink-dist-1.20.0.jar:::/opt/hadoop/conf:
    2025-02-20 08:18:28,907 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] - --------------------------------------------------------------------------------
    2025-02-20 08:18:28,908 INFO  org.apache.flink.kubernetes.taskmanager.KubernetesTaskExecutorRunner ] - Registered UNIX signal handlers for [TERM, HUP, INT]
    2025-02-20 08:18:28,919 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Using standard YAML parser to load flink configuration file from /opt/flink/conf/config.yaml.
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: blob.server.port, 6124
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: rest.flamegraph.enabled, true
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: jobmanager.execution.failover-strategy, region
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: jobmanager.rpc.address, localtest-session-cluster.flinkcdc
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: state.savepoints.dir, s3://tlake-ns2/flink/noname/savepoints
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.cluster-id, localtest-session-cluster
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.service-account, flink
    2025-02-20 08:18:28,962 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: s3.connection.ssl.enabled, false
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.hadoop.conf.config-map.name, flinkcdc-hive-site-cm
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: s3.secret-key, ******
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: s3.endpoint, http://10.10.27.23:9000
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.namespace, flinkcdc
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: parallelism.default, 2
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: taskmanager.numberOfTaskSlots, 4
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.rest-service.exposed.type, NodePort
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: env.java.opts.all, --add-exports=java.base/sun.net.util=ALL-UNNAMED --add-exports=java.rmi/sun.rmi.registry=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED --add-exports=java.security.jgss/sun.security.krb5=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.nio=ALL-UNNAMED --add-opens=java.base/sun.nio.ch=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.text=ALL-UNNAMED --add-opens=java.base/java.time=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.atomic=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.locks=ALL-UNNAMED
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.container.image.ref, registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle
    2025-02-20 08:18:28,963 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: historyserver.archive.fs.dir, s3a://tlake-ns2/flink/archives
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: jobmanager.archive.fs.dir, s3a://tlake-ns2/flink/archives
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: taskmanager.memory.process.size, 1728m
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.internal.jobmanager.entrypoint.class, org.apache.flink.kubernetes.entrypoint.KubernetesSessionClusterEntrypoint
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.artifacts.local-upload-enabled, true
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: web.cancel.enable, true
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: execution.target, kubernetes-session
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: jobmanager.memory.process.size, 1600m
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: jobmanager.rpc.port, 6123
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: taskmanager.rpc.port, 6122
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: kubernetes.pod-template-file.default, /Users/noname/Workspace/src/github/streaming/flinkcdc-oracle/doc/job/pod-template.yaml
    2025-02-20 08:18:28,964 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: rest.client.max-content-length, 209715200
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: s3.access-key, minioadmin
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: execution.checkpointing.interval, 10s
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: internal.cluster.execution-mode, NORMAL
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: web.submit.enable, true
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: rest.address, localhost
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: state.checkpoints.dir, s3://tlake-ns2/flink/noname/checkpoints
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading configuration property: s3.path.style.access, true
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.resource-id, localtest-session-cluster-taskmanager-1-1
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.network.min, 134217730b
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: jobmanager.memory.off-heap.size, 134217728b
    2025-02-20 08:18:28,965 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.cpu.cores, 4.0
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.task.off-heap.size, 0b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.jvm-metaspace.size, 268435456b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: jobmanager.memory.jvm-overhead.min, 201326592b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: external-resources, none
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: web.tmpdir, /tmp/flink-web-3d1ee81f-c16c-4464-97d0-8ac3d7cb6a3b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.jvm-overhead.min, 201326592b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.framework.off-heap.size, 134217728b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.network.max, 134217730b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.framework.heap.size, 134217728b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.managed.size, 536870920b
    2025-02-20 08:18:28,966 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.task.heap.size, 402653174b
    2025-02-20 08:18:28,967 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.numberOfTaskSlots, 4
    2025-02-20 08:18:28,967 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: taskmanager.memory.jvm-overhead.max, 201326592b
    2025-02-20 08:18:28,967 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: jobmanager.memory.jvm-metaspace.size, 268435456b
    2025-02-20 08:18:28,967 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: jobmanager.memory.heap.size, 1073741824b
    2025-02-20 08:18:28,967 INFO  org.apache.flink.configuration.GlobalConfiguration           ] - Loading dynamic configuration property: jobmanager.memory.jvm-overhead.max, 201326592b
    2025-02-20 08:18:29,014 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: external-resource-gpu
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-datadog
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-graphite
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-influx
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-jmx
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-prometheus
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-slf4j
    2025-02-20 08:18:29,018 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: metrics-statsd
    2025-02-20 08:18:29,019 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID not found, creating it: flink-s3-fs-hadoop-1.20.0
    2025-02-20 08:18:29,065 INFO  org.apache.flink.runtime.state.changelog.StateChangelogStorageLoader ] - StateChangelogStorageLoader initialized with shortcut names {memory,filesystem}.
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: external-resource-gpu
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-datadog
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-graphite
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-influx
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-jmx
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-prometheus
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-slf4j
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-statsd
    2025-02-20 08:18:29,065 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: flink-s3-fs-hadoop-1.20.0
    2025-02-20 08:18:29,067 INFO  org.apache.flink.runtime.state.changelog.StateChangelogStorageLoader ] - StateChangelogStorageLoader initialized with shortcut names {memory,filesystem}.
    2025-02-20 08:18:29,084 INFO  org.apache.flink.runtime.security.modules.HadoopModule       ] - Hadoop user set to flink (auth:SIMPLE)
    2025-02-20 08:18:29,085 INFO  org.apache.flink.runtime.security.modules.HadoopModule       ] - Kerberos security is disabled.
    2025-02-20 08:18:29,094 INFO  org.apache.flink.runtime.security.modules.JaasModule         ] - Jaas file will be created as /tmp/jaas-18324656113644331031.conf.
    2025-02-20 08:18:29,274 INFO  org.apache.flink.runtime.util.LeaderRetrievalUtils           ] - Trying to select the network interface and address to use by connecting to the leading JobManager.
    2025-02-20 08:18:29,275 INFO  org.apache.flink.runtime.util.LeaderRetrievalUtils           ] - TaskManager will try to connect for PT10S before falling back to heuristics
    2025-02-20 08:18:29,561 INFO  org.apache.flink.runtime.taskexecutor.TaskManagerRunner      ] - TaskManager will use hostname/address 'localtest-session-cluster-taskmanager-1-1' (10.233.67.247) for communication.
    2025-02-20 08:18:29,594 INFO  org.apache.flink.runtime.rpc.pekko.PekkoRpcServiceUtils      ] - Trying to start actor system, external address 10.233.67.247:6122, bind address 0.0.0.0:6122.
    2025-02-20 08:18:30,010 INFO  org.apache.pekko.event.slf4j.Slf4jLogger                     ] - Slf4jLogger started
    2025-02-20 08:18:30,032 INFO  org.apache.pekko.remote.RemoteActorRefProvider               ] - Pekko Cluster not in use - enabling unsafe features anyway because `pekko.remote.use-unsafe-remote-features-outside-cluster` has been enabled.
    2025-02-20 08:18:30,032 INFO  org.apache.pekko.remote.Remoting                             ] - Starting remoting
    2025-02-20 08:18:30,144 INFO  org.apache.pekko.remote.Remoting                             ] - Remoting started; listening on addresses :[pekko.tcp://flink@10.233.67.247:6122]
    2025-02-20 08:18:30,236 INFO  org.apache.flink.runtime.rpc.pekko.PekkoRpcServiceUtils      ] - Actor system started at pekko.tcp://flink@10.233.67.247:6122
    2025-02-20 08:18:30,248 INFO  org.apache.flink.runtime.taskexecutor.TaskManagerRunner      ] - Using working directory: WorkingDirectory(/tmp/tm_localtest-session-cluster-taskmanager-1-1)
    2025-02-20 08:18:30,256 INFO  org.apache.flink.runtime.metrics.MetricRegistryImpl          ] - No metrics reporter configured, no metrics will be exposed/reported.
    2025-02-20 08:18:30,256 INFO  org.apache.flink.runtime.metrics.MetricRegistryImpl          ] - No trace reporter configured, no metrics will be exposed/reported.
    2025-02-20 08:18:30,260 INFO  org.apache.flink.runtime.rpc.pekko.PekkoRpcServiceUtils      ] - Trying to start actor system, external address 10.233.67.247:0, bind address 0.0.0.0:0.
    2025-02-20 08:18:30,273 INFO  org.apache.pekko.event.slf4j.Slf4jLogger                     ] - Slf4jLogger started
    2025-02-20 08:18:30,275 INFO  org.apache.pekko.remote.RemoteActorRefProvider               ] - Pekko Cluster not in use - enabling unsafe features anyway because `pekko.remote.use-unsafe-remote-features-outside-cluster` has been enabled.
    2025-02-20 08:18:30,276 INFO  org.apache.pekko.remote.Remoting                             ] - Starting remoting
    2025-02-20 08:18:30,283 INFO  org.apache.pekko.remote.Remoting                             ] - Remoting started; listening on addresses :[pekko.tcp://flink-metrics@10.233.67.247:34641]
    2025-02-20 08:18:30,289 INFO  org.apache.flink.runtime.rpc.pekko.PekkoRpcServiceUtils      ] - Actor system started at pekko.tcp://flink-metrics@10.233.67.247:34641
    2025-02-20 08:18:30,298 INFO  org.apache.flink.runtime.rpc.pekko.PekkoRpcService           ] - Starting RPC endpoint for org.apache.flink.runtime.metrics.dump.MetricQueryService at pekko://flink-metrics/user/rpc/MetricQueryService_localtest-session-cluster-taskmanager-1-1 .
    2025-02-20 08:18:30,346 INFO  org.apache.flink.runtime.blob.PermanentBlobCache             ] - Created BLOB cache storage directory /tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage
    2025-02-20 08:18:30,349 INFO  org.apache.flink.runtime.blob.TransientBlobCache             ] - Created BLOB cache storage directory /tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage
    2025-02-20 08:18:30,352 INFO  org.apache.flink.runtime.externalresource.ExternalResourceUtils ] - Enabled external resources: ]
    2025-02-20 08:18:30,352 INFO  org.apache.flink.runtime.security.token.DelegationTokenReceiverRepository ] - Loading delegation token receivers
    2025-02-20 08:18:30,355 INFO  org.apache.flink.runtime.security.token.DelegationTokenReceiverRepository ] - Delegation token receiver HiveServer2 loaded and initialized
    2025-02-20 08:18:30,356 INFO  org.apache.flink.runtime.security.token.DelegationTokenReceiverRepository ] - Delegation token receiver hadoopfs loaded and initialized
    2025-02-20 08:18:30,356 INFO  org.apache.flink.runtime.security.token.DelegationTokenReceiverRepository ] - Delegation token receiver hbase loaded and initialized
    2025-02-20 08:18:30,356 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: external-resource-gpu
    2025-02-20 08:18:30,356 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-datadog
    2025-02-20 08:18:30,356 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-graphite
    2025-02-20 08:18:30,356 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-influx
    2025-02-20 08:18:30,357 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-jmx
    2025-02-20 08:18:30,357 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-prometheus
    2025-02-20 08:18:30,357 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-slf4j
    2025-02-20 08:18:30,357 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: metrics-statsd
    2025-02-20 08:18:30,357 INFO  org.apache.flink.core.plugin.DefaultPluginManager            ] - Plugin loader with ID found, reusing it: flink-s3-fs-hadoop-1.20.0
    2025-02-20 08:18:30,358 INFO  org.apache.flink.runtime.security.token.DelegationTokenReceiverRepository ] - Delegation token receiver s3-hadoop loaded and initialized
    2025-02-20 08:18:30,358 INFO  org.apache.flink.runtime.security.token.DelegationTokenReceiverRepository ] - Delegation token receivers loaded successfully
    2025-02-20 08:18:30,359 INFO  org.apache.flink.runtime.taskexecutor.TaskManagerRunner      ] - Starting TaskManager with ResourceID: localtest-session-cluster-taskmanager-1-1
    2025-02-20 08:18:30,379 INFO  org.apache.flink.runtime.taskexecutor.TaskManagerServices    ] - Temporary file directory '/tmp': total 245 GB, usable 62 GB (25.31% usable)
    2025-02-20 08:18:30,382 INFO  org.apache.flink.runtime.io.disk.iomanager.IOManager         ] - Created a new FileChannelManager for spilling of task related data to disk (joins, sorting, ...). Used directories:
        /tmp/flink-io-2a28f14b-f3fd-4b92-a790-a4a44a8b2444
    2025-02-20 08:18:30,389 INFO  org.apache.flink.runtime.io.network.netty.NettyConfig        ] - NettyConfig [server address: /0.0.0.0, server port range: 0, ssl enabled: false, memory segment size (bytes): 32768, transport type: AUTO, number of server threads: 4 (manual), number of client threads: 4 (manual), server connect backlog: 0 (use Netty's default), client connect timeout (sec): 120, send/receive buffer size (bytes): 0 (use Netty's default)]
    2025-02-20 08:18:30,438 INFO  org.apache.flink.runtime.io.network.NettyShuffleServiceFactory ] - Created a new FileChannelManager for storing result partitions of BLOCKING shuffles. Used directories:
        /tmp/flink-netty-shuffle-5c5421fa-591d-4606-b490-6cbe5b71ad61
    2025-02-20 08:18:30,520 INFO  org.apache.flink.runtime.io.network.buffer.NetworkBufferPool ] - Allocated 128 MB for network buffer pool (number of memory segments: 4096, bytes per segment: 32768).
    2025-02-20 08:18:30,531 INFO  org.apache.flink.runtime.io.network.NettyShuffleEnvironment  ] - Starting the network environment and its components.
    2025-02-20 08:18:30,576 INFO  org.apache.flink.runtime.io.network.netty.NettyClient        ] - Transport type 'auto': using EPOLL.
    2025-02-20 08:18:30,577 INFO  org.apache.flink.runtime.io.network.netty.NettyClient        ] - Successful initialization (took 45 ms).
    2025-02-20 08:18:30,580 INFO  org.apache.flink.runtime.io.network.netty.NettyServer        ] - Transport type 'auto': using EPOLL.
    2025-02-20 08:18:30,602 INFO  org.apache.flink.runtime.io.network.netty.NettyServer        ] - Successful initialization (took 24 ms). Listening on SocketAddress /0:0:0:0:0:0:0:0%0:42095.
    2025-02-20 08:18:30,602 INFO  org.apache.flink.runtime.taskexecutor.TaskManagerServices    ] - TaskManager data connection initialized successfully; listening internally on port: 42095
    2025-02-20 08:18:30,603 INFO  org.apache.flink.runtime.taskexecutor.KvStateService         ] - Starting the kvState service and its components.
    2025-02-20 08:18:30,638 INFO  org.apache.flink.runtime.rpc.pekko.PekkoRpcService           ] - Starting RPC endpoint for org.apache.flink.runtime.taskexecutor.TaskExecutor at pekko://flink/user/rpc/taskmanager_0 .
    2025-02-20 08:18:30,651 INFO  org.apache.flink.runtime.taskexecutor.DefaultJobLeaderService ] - Start job leader service.
    2025-02-20 08:18:30,653 INFO  org.apache.flink.runtime.filecache.FileCache                 ] - User file cache uses directory /tmp/flink-dist-cache-1bbb5bca-5421-49ff-9019-b43577692c15
    2025-02-20 08:18:30,655 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Connecting to ResourceManager pekko.tcp://flink@localtest-session-cluster.flinkcdc:6123/user/rpc/resourcemanager_*(00000000000000000000000000000000).
    2025-02-20 08:18:30,799 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Resolved ResourceManager address, beginning registration
    2025-02-20 08:18:30,854 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Successful registration at resource manager pekko.tcp://flink@localtest-session-cluster.flinkcdc:6123/user/rpc/resourcemanager_* under registration id 28299129f4cf99bda7612a606940dfad.
    2025-02-20 08:18:30,870 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Receive slot request 4c517b4b4968a55450482a0d87c5af77 for job e12c802e143177bdd6823136c3646c1c from resource manager with leader id 00000000000000000000000000000000.
    2025-02-20 08:18:30,871 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Allocated slot for 4c517b4b4968a55450482a0d87c5af77 with resources ResourceProfile{cpuCores=1, taskHeapMemory=96.000mb (100663293 bytes), taskOffHeapMemory=0 bytes, managedMemory=128.000mb (134217730 bytes), networkMemory=32.000mb (33554432 bytes)}.
    2025-02-20 08:18:30,875 INFO  org.apache.flink.runtime.taskexecutor.DefaultJobLeaderService ] - Add job e12c802e143177bdd6823136c3646c1c for job leader monitoring.
    2025-02-20 08:18:30,877 INFO  org.apache.flink.runtime.taskexecutor.DefaultJobLeaderService ] - Try to register at job manager pekko.tcp://flink@localtest-session-cluster.flinkcdc:6123/user/rpc/jobmanager_2 with leader id 00000000-0000-0000-0000-000000000000.
    2025-02-20 08:18:30,890 INFO  org.apache.flink.runtime.taskexecutor.DefaultJobLeaderService ] - Resolved JobManager address, beginning registration
    2025-02-20 08:18:30,904 INFO  org.apache.flink.runtime.taskexecutor.DefaultJobLeaderService ] - Successful registration at job manager pekko.tcp://flink@localtest-session-cluster.flinkcdc:6123/user/rpc/jobmanager_2 for job e12c802e143177bdd6823136c3646c1c.
    2025-02-20 08:18:30,905 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Establish JobManager connection for job e12c802e143177bdd6823136c3646c1c.
    2025-02-20 08:18:30,907 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Offer reserved slots to the leader of job e12c802e143177bdd6823136c3646c1c.
    2025-02-20 08:18:30,980 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:30,995 INFO  org.apache.flink.runtime.state.changelog.StateChangelogStorageLoader ] - Creating a changelog storage with name 'memory'.
    2025-02-20 08:18:31,001 INFO  org.apache.flink.runtime.state.TaskExecutorChannelStateExecutorFactoryManager ] - Creating the channel state executor factory for job id e12c802e143177bdd6823136c3646c1c
    2025-02-20 08:18:31,010 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Received task Source: Custom Source (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_0), deploy into slot with allocation id 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:31,012 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Source: Custom Source (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_0) switched from CREATED to DEPLOYING.
    2025-02-20 08:18:31,015 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:31,016 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Loading JAR files for task Source: Custom Source (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_0) [DEPLOYING].
    2025-02-20 08:18:31,019 INFO  org.apache.flink.runtime.blob.BlobClient                     ] - Downloading e12c802e143177bdd6823136c3646c1c/p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4 from localtest-session-cluster.flinkcdc/10.233.67.92:6124
    2025-02-20 08:18:31,030 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Received task IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0), deploy into slot with allocation id 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:31,031 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:31,031 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0) switched from CREATED to DEPLOYING.
    2025-02-20 08:18:31,031 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Loading JAR files for task IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0) [DEPLOYING].
    2025-02-20 08:18:31,035 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Received task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0), deploy into slot with allocation id 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:31,035 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0) switched from CREATED to DEPLOYING.
    2025-02-20 08:18:31,036 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:31,036 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Loading JAR files for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0) [DEPLOYING].
    2025-02-20 08:18:31,061 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - State backend is set to heap memory org.apache.flink.runtime.state.hashmap.HashMapStateBackend@40332439
    2025-02-20 08:18:31,061 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - State backend is set to heap memory org.apache.flink.runtime.state.hashmap.HashMapStateBackend@5d381f9f
    2025-02-20 08:18:31,061 INFO  org.apache.flink.runtime.state.StateBackendLoader            ] - State backend loader loads the state backend as HashMapStateBackend
    2025-02-20 08:18:31,061 INFO  org.apache.flink.runtime.state.StateBackendLoader            ] - State backend loader loads the state backend as HashMapStateBackend
    2025-02-20 08:18:31,066 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - Using job/cluster config to configure application-defined checkpoint storage: org.apache.flink.runtime.state.storage.FileSystemCheckpointStorage@838de6a0
    2025-02-20 08:18:31,066 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - Using job/cluster config to configure application-defined checkpoint storage: org.apache.flink.runtime.state.storage.FileSystemCheckpointStorage@838de6a0
    2025-02-20 08:18:31,067 WARN  org.apache.flink.configuration.Configuration                 ] - Config uses deprecated configuration key 'state.savepoints.dir' instead of proper key 'execution.checkpointing.savepoint-dir'
    2025-02-20 08:18:31,067 WARN  org.apache.flink.configuration.Configuration                 ] - Config uses deprecated configuration key 'state.savepoints.dir' instead of proper key 'execution.checkpointing.savepoint-dir'
    2025-02-20 08:18:31,066 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - Source: Custom Source (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_0) switched from DEPLOYING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Could not instantiate outputs in order.
        at org.apache.flink.streaming.api.graph.StreamConfig.getVertexNonChainedOutputs(StreamConfig.java:590) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.createRecordWriters(StreamTask.java:1743) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.createRecordWriterDelegate(StreamTask.java:1727) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.<init>(StreamTask.java:428) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.<init>(StreamTask.java:380) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.SourceStreamTask.<init>(SourceStreamTask.java:108) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.SourceStreamTask.<init>(SourceStreamTask.java:104) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method) ~[?:?]
        at jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingConstructorAccessorImpl.newInstance(Unknown Source) ~[?:?]
        at java.lang.reflect.Constructor.newInstance(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.taskmanager.Task.loadAndInstantiateInvokable(Task.java:1636) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:749) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.EqualityFieldKeySelector
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.util.ArrayList.readObject(Unknown Source) ~[?:?]
        at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:?]
        at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at java.io.ObjectStreamClass.invokeReadObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getVertexNonChainedOutputs(StreamConfig.java:586) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 14 more
    2025-02-20 08:18:31,071 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for Source: Custom Source (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_0).
    2025-02-20 08:18:31,078 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task Source: Custom Source (1/1)#0 43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_0.
    2025-02-20 08:18:31,118 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to cancel task IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0).
    2025-02-20 08:18:31,118 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0) switched from DEPLOYING to CANCELING.
    2025-02-20 08:18:31,118 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to cancel task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0).
    2025-02-20 08:18:31,119 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0) switched from DEPLOYING to CANCELING.
    2025-02-20 08:18:31,124 INFO  org.apache.flink.fs.s3.common.token.AbstractS3DelegationTokenReceiver ] - Updating Hadoop configuration
    2025-02-20 08:18:31,125 INFO  org.apache.flink.fs.s3.common.token.AbstractS3DelegationTokenReceiver ] - Updated Hadoop configuration successfully
    2025-02-20 08:18:31,250 WARN  org.apache.hadoop.metrics2.impl.MetricsConfig                ] - Cannot locate configuration: tried hadoop-metrics2-s3a-file-system.properties,hadoop-metrics2.properties
    2025-02-20 08:18:31,266 INFO  org.apache.hadoop.metrics2.impl.MetricsSystemImpl            ] - Scheduled Metric snapshot period at 10 second(s).
    2025-02-20 08:18:31,266 INFO  org.apache.hadoop.metrics2.impl.MetricsSystemImpl            ] - s3a-file-system metrics system started
    2025-02-20 08:18:31,292 WARN  org.apache.hadoop.util.NativeCodeLoader                      ] - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
    2025-02-20 08:18:31,822 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0) switched from CANCELING to CANCELED.
    2025-02-20 08:18:31,822 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0) switched from CANCELING to CANCELED.
    2025-02-20 08:18:31,822 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0).
    2025-02-20 08:18:31,822 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#0 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0).
    2025-02-20 08:18:31,823 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state CANCELED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#0 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_0.
    2025-02-20 08:18:31,824 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state CANCELED to JobManager for task IcebergStreamWriter (1/1)#0 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_0.
    2025-02-20 08:18:32,129 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:32,131 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Received task Source: Custom Source (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_1), deploy into slot with allocation id 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:32,132 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:32,132 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Source: Custom Source (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_1) switched from CREATED to DEPLOYING.
    2025-02-20 08:18:32,132 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Loading JAR files for task Source: Custom Source (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_1) [DEPLOYING].
    2025-02-20 08:18:32,134 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Received task IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1), deploy into slot with allocation id 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:32,135 INFO  org.apache.flink.runtime.taskexecutor.slot.TaskSlotTableImpl ] - Activate slot 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:32,135 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1) switched from CREATED to DEPLOYING.
    2025-02-20 08:18:32,135 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - Source: Custom Source (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_1) switched from DEPLOYING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Could not instantiate outputs in order.
        at org.apache.flink.streaming.api.graph.StreamConfig.getVertexNonChainedOutputs(StreamConfig.java:590) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.createRecordWriters(StreamTask.java:1743) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.createRecordWriterDelegate(StreamTask.java:1727) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.<init>(StreamTask.java:428) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.<init>(StreamTask.java:380) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.SourceStreamTask.<init>(SourceStreamTask.java:108) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.SourceStreamTask.<init>(SourceStreamTask.java:104) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method) ~[?:?]
        at jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingConstructorAccessorImpl.newInstance(Unknown Source) ~[?:?]
        at java.lang.reflect.Constructor.newInstance(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.taskmanager.Task.loadAndInstantiateInvokable(Task.java:1636) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:749) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.EqualityFieldKeySelector
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.util.ArrayList.readObject(Unknown Source) ~[?:?]
        at jdk.internal.reflect.GeneratedMethodAccessor17.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at java.io.ObjectStreamClass.invokeReadObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getVertexNonChainedOutputs(StreamConfig.java:586) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 14 more
    2025-02-20 08:18:32,135 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Loading JAR files for task IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1) [DEPLOYING].
    2025-02-20 08:18:32,135 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for Source: Custom Source (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_1).
    2025-02-20 08:18:32,137 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - State backend is set to heap memory org.apache.flink.runtime.state.hashmap.HashMapStateBackend@5a6c5064
    2025-02-20 08:18:32,137 INFO  org.apache.flink.runtime.state.StateBackendLoader            ] - State backend loader loads the state backend as HashMapStateBackend
    2025-02-20 08:18:32,138 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - Using job/cluster config to configure application-defined checkpoint storage: org.apache.flink.runtime.state.storage.FileSystemCheckpointStorage@838de6a0
    2025-02-20 08:18:32,138 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Received task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1), deploy into slot with allocation id 4c517b4b4968a55450482a0d87c5af77.
    2025-02-20 08:18:32,139 WARN  org.apache.flink.configuration.Configuration                 ] - Config uses deprecated configuration key 'state.savepoints.dir' instead of proper key 'execution.checkpointing.savepoint-dir'
    2025-02-20 08:18:32,139 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1) switched from DEPLOYING to INITIALIZING.
    2025-02-20 08:18:32,139 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task Source: Custom Source (1/1)#1 43202a2ada44e627b1d2fd8dc8c9eba7_10d8117fcd3a1e643cbc6fd935cbd6de_0_1.
    2025-02-20 08:18:32,139 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1) switched from CREATED to DEPLOYING.
    2025-02-20 08:18:32,139 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Loading JAR files for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1) [DEPLOYING].
    2025-02-20 08:18:32,140 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - State backend is set to heap memory org.apache.flink.runtime.state.hashmap.HashMapStateBackend@7d305d82
    2025-02-20 08:18:32,141 INFO  org.apache.flink.runtime.state.StateBackendLoader            ] - State backend loader loads the state backend as HashMapStateBackend
    2025-02-20 08:18:32,141 INFO  org.apache.flink.streaming.runtime.tasks.StreamTask          ] - Using job/cluster config to configure application-defined checkpoint storage: org.apache.flink.runtime.state.storage.FileSystemCheckpointStorage@838de6a0
    2025-02-20 08:18:32,141 WARN  org.apache.flink.configuration.Configuration                 ] - Config uses deprecated configuration key 'state.savepoints.dir' instead of proper key 'execution.checkpointing.savepoint-dir'
    2025-02-20 08:18:32,141 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1) switched from DEPLOYING to INITIALIZING.
    2025-02-20 08:18:32,148 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to cancel task IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1).
    2025-02-20 08:18:32,148 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1) switched from INITIALIZING to CANCELING.
    2025-02-20 08:18:32,148 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Triggering cancellation of task code IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1).
    2025-02-20 08:18:32,151 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to cancel task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1).
    2025-02-20 08:18:32,151 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1) switched from INITIALIZING to CANCELING.
    2025-02-20 08:18:32,151 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Triggering cancellation of task code IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1).
    2025-02-20 08:18:32,153 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to cancel task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1).
    2025-02-20 08:18:32,153 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 is already in state CANCELING
    2025-02-20 08:18:32,154 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to fail task externally IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1).
    2025-02-20 08:18:32,154 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 is already in state CANCELING
    2025-02-20 08:18:32,159 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1) switched from CANCELING to CANCELED.
    2025-02-20 08:18:32,159 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1).
    2025-02-20 08:18:32,160 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state CANCELED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#1 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_1.
    2025-02-20 08:18:32,161 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1) switched from CANCELING to CANCELED.
    2025-02-20 08:18:32,161 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#1 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1).
    2025-02-20 08:18:32,161 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state CANCELED to JobManager for task IcebergStreamWriter (1/1)#1 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_1.
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:33,755 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#2 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_2) switched from INITIALIZING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Cannot load user class: org.apache.iceberg.flink.sink.IcebergFilesCommitter
    ClassLoader info: URL ClassLoader:
        file: '/tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage/job_e12c802e143177bdd6823136c3646c1c/blob_p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4' (valid JAR)
    Class not resolvable through given classloader.
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:414) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.OperatorChain.<init>(OperatorChain.java:169) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.RegularOperatorChain.<init>(RegularOperatorChain.java:60) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restoreInternal(StreamTask.java:789) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restore(StreamTask.java:771) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.runWithSystemExitMonitoring(Task.java:970) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.restoreAndInvoke(Task.java:939) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:763) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.IcebergFilesCommitter
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:33,755 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#2 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_2).
    2025-02-20 08:18:33,756 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#2 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_2).
    2025-02-20 08:18:33,758 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergStreamWriter (1/1)#2 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_2.
    2025-02-20 08:18:33,760 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#2 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_2.
    2025-02-20 08:18:33,762 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Cannot find task to fail for execution 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_2 with exception:
    org.apache.flink.runtime.jobmaster.ExecutionGraphException: The execution attempt 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_2 was not found.
        at org.apache.flink.runtime.jobmaster.JobMaster.updateTaskExecutionState(JobMaster.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:?]
        at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.lambda$handleRpcInvocation$1(PekkoRpcActor.java:318) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.concurrent.ClassLoadingUtils.runWithContextClassLoader(ClassLoadingUtils.java:83) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcInvocation(PekkoRpcActor.java:316) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcMessage(PekkoRpcActor.java:229) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.FencedPekkoRpcActor.handleRpcMessage(FencedPekkoRpcActor.java:88) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleMessage(PekkoRpcActor.java:174) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:33) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse(PartialFunction.scala:127) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse$(PartialFunction.scala:126) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.applyOrElse(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:175) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive(Actor.scala:547) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive$(Actor.scala:545) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.AbstractActor.aroundReceive(AbstractActor.scala:229) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.receiveMessage(ActorCell.scala:590) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.invoke(ActorCell.scala:557) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.processMailbox(Mailbox.scala:280) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.run(Mailbox.scala:241) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.exec(Mailbox.scala:253) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.scan(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.runWorker(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source) [?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:36,126 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#3 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_3).
    2025-02-20 08:18:36,126 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#3 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_3) switched from INITIALIZING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Cannot load user class: org.apache.iceberg.flink.sink.IcebergFilesCommitter
    ClassLoader info: URL ClassLoader:
        file: '/tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage/job_e12c802e143177bdd6823136c3646c1c/blob_p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4' (valid JAR)
    Class not resolvable through given classloader.
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:414) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.OperatorChain.<init>(OperatorChain.java:169) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.RegularOperatorChain.<init>(RegularOperatorChain.java:60) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restoreInternal(StreamTask.java:789) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restore(StreamTask.java:771) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.runWithSystemExitMonitoring(Task.java:970) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.restoreAndInvoke(Task.java:939) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:763) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.IcebergFilesCommitter
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:36,127 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#3 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_3).
    2025-02-20 08:18:36,127 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergStreamWriter (1/1)#3 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_3.
    2025-02-20 08:18:36,128 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#3 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_3.
    2025-02-20 08:18:36,131 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Cannot find task to fail for execution 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_3 with exception:
    org.apache.flink.runtime.jobmaster.ExecutionGraphException: The execution attempt 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_3 was not found.
        at org.apache.flink.runtime.jobmaster.JobMaster.updateTaskExecutionState(JobMaster.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:?]
        at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.lambda$handleRpcInvocation$1(PekkoRpcActor.java:318) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.concurrent.ClassLoadingUtils.runWithContextClassLoader(ClassLoadingUtils.java:83) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcInvocation(PekkoRpcActor.java:316) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcMessage(PekkoRpcActor.java:229) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.FencedPekkoRpcActor.handleRpcMessage(FencedPekkoRpcActor.java:88) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleMessage(PekkoRpcActor.java:174) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:33) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse(PartialFunction.scala:127) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse$(PartialFunction.scala:126) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.applyOrElse(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:175) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive(Actor.scala:547) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive$(Actor.scala:545) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.AbstractActor.aroundReceive(AbstractActor.scala:229) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.receiveMessage(ActorCell.scala:590) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.invoke(ActorCell.scala:557) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.processMailbox(Mailbox.scala:280) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.run(Mailbox.scala:241) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.exec(Mailbox.scala:253) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.scan(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.runWorker(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source) [?:?]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:39,772 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#4 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_4) switched from INITIALIZING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Cannot load user class: org.apache.iceberg.flink.sink.IcebergStreamWriter
    ClassLoader info: URL ClassLoader:
        file: '/tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage/job_e12c802e143177bdd6823136c3646c1c/blob_p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4' (valid JAR)
    Class not resolvable through given classloader.
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:414) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.OperatorChain.<init>(OperatorChain.java:169) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.RegularOperatorChain.<init>(RegularOperatorChain.java:60) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restoreInternal(StreamTask.java:789) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restore(StreamTask.java:771) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.runWithSystemExitMonitoring(Task.java:970) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.restoreAndInvoke(Task.java:939) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:763) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.IcebergStreamWriter
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:39,772 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#4 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_4).
    2025-02-20 08:18:39,772 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#4 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_4).
    2025-02-20 08:18:39,773 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#4 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_4.
    2025-02-20 08:18:39,774 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergStreamWriter (1/1)#4 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_4.
    2025-02-20 08:18:39,778 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Cannot find task to fail for execution 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_4 with exception:
    org.apache.flink.runtime.jobmaster.ExecutionGraphException: The execution attempt 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_4 was not found.
        at org.apache.flink.runtime.jobmaster.JobMaster.updateTaskExecutionState(JobMaster.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.GeneratedMethodAccessor95.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.lambda$handleRpcInvocation$1(PekkoRpcActor.java:318) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.concurrent.ClassLoadingUtils.runWithContextClassLoader(ClassLoadingUtils.java:83) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcInvocation(PekkoRpcActor.java:316) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcMessage(PekkoRpcActor.java:229) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.FencedPekkoRpcActor.handleRpcMessage(FencedPekkoRpcActor.java:88) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleMessage(PekkoRpcActor.java:174) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:33) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse(PartialFunction.scala:127) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse$(PartialFunction.scala:126) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.applyOrElse(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:175) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive(Actor.scala:547) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive$(Actor.scala:545) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.AbstractActor.aroundReceive(AbstractActor.scala:229) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.receiveMessage(ActorCell.scala:590) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.invoke(ActorCell.scala:557) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.processMailbox(Mailbox.scala:280) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.run(Mailbox.scala:241) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.exec(Mailbox.scala:253) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.scan(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.runWorker(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source) [?:?]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:44,644 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#5 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_5) switched from INITIALIZING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Cannot load user class: org.apache.iceberg.flink.sink.IcebergStreamWriter
    ClassLoader info: URL ClassLoader:
        file: '/tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage/job_e12c802e143177bdd6823136c3646c1c/blob_p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4' (valid JAR)
    Class not resolvable through given classloader.
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:414) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.OperatorChain.<init>(OperatorChain.java:169) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.RegularOperatorChain.<init>(RegularOperatorChain.java:60) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restoreInternal(StreamTask.java:789) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restore(StreamTask.java:771) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.runWithSystemExitMonitoring(Task.java:970) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.restoreAndInvoke(Task.java:939) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:763) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.IcebergStreamWriter
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:44,644 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#5 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_5).
    2025-02-20 08:18:44,644 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#5 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_5).
    2025-02-20 08:18:44,645 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#5 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_5.
    2025-02-20 08:18:44,646 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergStreamWriter (1/1)#5 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_5.
    2025-02-20 08:18:44,649 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Cannot find task to fail for execution 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_5 with exception:
    org.apache.flink.runtime.jobmaster.ExecutionGraphException: The execution attempt 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_5 was not found.
        at org.apache.flink.runtime.jobmaster.JobMaster.updateTaskExecutionState(JobMaster.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.GeneratedMethodAccessor95.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.lambda$handleRpcInvocation$1(PekkoRpcActor.java:318) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.concurrent.ClassLoadingUtils.runWithContextClassLoader(ClassLoadingUtils.java:83) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcInvocation(PekkoRpcActor.java:316) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcMessage(PekkoRpcActor.java:229) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.FencedPekkoRpcActor.handleRpcMessage(FencedPekkoRpcActor.java:88) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleMessage(PekkoRpcActor.java:174) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:33) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse(PartialFunction.scala:127) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse$(PartialFunction.scala:126) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.applyOrElse(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:175) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive(Actor.scala:547) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive$(Actor.scala:545) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.AbstractActor.aroundReceive(AbstractActor.scala:229) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.receiveMessage(ActorCell.scala:590) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.invoke(ActorCell.scala:557) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.processMailbox(Mailbox.scala:280) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.run(Mailbox.scala:241) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.exec(Mailbox.scala:253) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.scan(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.runWorker(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source) [?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:52,242 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#6 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_6).
    2025-02-20 08:18:52,243 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#6 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_6.
    2025-02-20 08:18:52,242 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergStreamWriter (1/1)#6 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_6) switched from INITIALIZING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Cannot load user class: org.apache.iceberg.flink.sink.IcebergStreamWriter
    ClassLoader info: URL ClassLoader:
        file: '/tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage/job_e12c802e143177bdd6823136c3646c1c/blob_p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4' (valid JAR)
    Class not resolvable through given classloader.
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:414) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.OperatorChain.<init>(OperatorChain.java:169) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.RegularOperatorChain.<init>(RegularOperatorChain.java:60) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restoreInternal(StreamTask.java:789) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restore(StreamTask.java:771) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.runWithSystemExitMonitoring(Task.java:970) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.restoreAndInvoke(Task.java:939) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:763) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.IcebergStreamWriter
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:18:52,244 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#6 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_6).
    2025-02-20 08:18:52,245 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Attempting to cancel task IcebergStreamWriter (1/1)#6 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_6).
    2025-02-20 08:18:52,245 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Task IcebergStreamWriter (1/1)#6 is already in state FAILED
    2025-02-20 08:18:52,246 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergStreamWriter (1/1)#6 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_6.
    2025-02-20 08:18:52,246 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Cannot find task to fail for execution 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_6 with exception:
    org.apache.flink.runtime.jobmaster.ExecutionGraphException: The execution attempt 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_6 was not found.
        at org.apache.flink.runtime.jobmaster.JobMaster.updateTaskExecutionState(JobMaster.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.GeneratedMethodAccessor95.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.lambda$handleRpcInvocation$1(PekkoRpcActor.java:318) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.concurrent.ClassLoadingUtils.runWithContextClassLoader(ClassLoadingUtils.java:83) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcInvocation(PekkoRpcActor.java:316) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcMessage(PekkoRpcActor.java:229) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.FencedPekkoRpcActor.handleRpcMessage(FencedPekkoRpcActor.java:88) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleMessage(PekkoRpcActor.java:174) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:33) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse(PartialFunction.scala:127) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse$(PartialFunction.scala:126) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.applyOrElse(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:175) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive(Actor.scala:547) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive$(Actor.scala:545) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.AbstractActor.aroundReceive(AbstractActor.scala:229) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.receiveMessage(ActorCell.scala:590) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.invoke(ActorCell.scala:557) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.processMailbox(Mailbox.scala:280) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.run(Mailbox.scala:241) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.exec(Mailbox.scala:253) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.scan(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.runWorker(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source) [?:?]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:19:04,280 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergStreamWriter (1/1)#7 (43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_7).
    2025-02-20 08:19:04,280 WARN  org.apache.flink.runtime.taskmanager.Task                    ] - IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#7 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_7) switched from INITIALIZING to FAILED with failure cause:
    org.apache.flink.streaming.runtime.tasks.StreamTaskException: Cannot load user class: org.apache.iceberg.flink.sink.IcebergFilesCommitter
    ClassLoader info: URL ClassLoader:
        file: '/tmp/tm_localtest-session-cluster-taskmanager-1-1/blobStorage/job_e12c802e143177bdd6823136c3646c1c/blob_p-e862f06b4f069557ef1a07a9aae51d865863ef01-30a919918d4bfc534c52472ab055a9e4' (valid JAR)
    Class not resolvable through given classloader.
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:414) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.OperatorChain.<init>(OperatorChain.java:169) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.RegularOperatorChain.<init>(RegularOperatorChain.java:60) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restoreInternal(StreamTask.java:789) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.runtime.tasks.StreamTask.restore(StreamTask.java:771) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.runWithSystemExitMonitoring(Task.java:970) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.restoreAndInvoke(Task.java:939) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.doRun(Task.java:763) [flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.taskmanager.Task.run(Task.java:575) [flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Thread.run(Unknown Source) [?:?]
    Caused by: java.lang.ClassNotFoundException: org.apache.iceberg.flink.sink.IcebergFilesCommitter
        at java.net.URLClassLoader.findClass(Unknown Source) ~[?:?]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:74) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.ClassLoader.loadClass(Unknown Source) ~[?:?]
        at org.apache.flink.util.FlinkUserCodeClassLoaders$SafetyNetWrapperClassLoader.loadClass(FlinkUserCodeClassLoaders.java:197) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.lang.Class.forName0(Native Method) ~[?:?]
        at java.lang.Class.forName(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil$ClassLoaderObjectInputStream.resolveClass(InstantiationUtil.java:78) ~[flink-dist-1.20.0.jar:1.20.0]
        at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readClassDesc(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.defaultReadFields(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readSerialData(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject0(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at java.io.ObjectInputStream.readObject(Unknown Source) ~[?:?]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:533) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.deserializeObject(InstantiationUtil.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.util.InstantiationUtil.readObjectFromConfig(InstantiationUtil.java:475) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.streaming.api.graph.StreamConfig.getStreamOperatorFactory(StreamConfig.java:400) ~[flink-dist-1.20.0.jar:1.20.0]
        ... 9 more
    2025-02-20 08:19:04,281 INFO  org.apache.flink.runtime.taskmanager.Task                    ] - Freeing task resources for IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#7 (43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_7).
    2025-02-20 08:19:04,282 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergStreamWriter (1/1)#7 43202a2ada44e627b1d2fd8dc8c9eba7_a6ead934a4b597cc7ccac2e053da22a1_0_7.
    2025-02-20 08:19:04,283 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Un-registering task and sending final execution state FAILED to JobManager for task IcebergFilesCommitter -> Sink: IcebergSink iceberg_hive.soe.test_number_table (1/1)#7 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_7.
    2025-02-20 08:19:04,285 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           ] - Cannot find task to fail for execution 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_7 with exception:
    org.apache.flink.runtime.jobmaster.ExecutionGraphException: The execution attempt 43202a2ada44e627b1d2fd8dc8c9eba7_4ba5e0de9b9bf0a19d7b94f11cf285ed_0_7 was not found.
        at org.apache.flink.runtime.jobmaster.JobMaster.updateTaskExecutionState(JobMaster.java:521) ~[flink-dist-1.20.0.jar:1.20.0]
        at jdk.internal.reflect.GeneratedMethodAccessor95.invoke(Unknown Source) ~[?:?]
        at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source) ~[?:?]
        at java.lang.reflect.Method.invoke(Unknown Source) ~[?:?]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.lambda$handleRpcInvocation$1(PekkoRpcActor.java:318) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.concurrent.ClassLoadingUtils.runWithContextClassLoader(ClassLoadingUtils.java:83) ~[flink-dist-1.20.0.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcInvocation(PekkoRpcActor.java:316) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleRpcMessage(PekkoRpcActor.java:229) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.FencedPekkoRpcActor.handleRpcMessage(FencedPekkoRpcActor.java:88) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.flink.runtime.rpc.pekko.PekkoRpcActor.handleMessage(PekkoRpcActor.java:174) ~[flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:33) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.apply(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse(PartialFunction.scala:127) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction.applyOrElse$(PartialFunction.scala:126) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.japi.pf.UnitCaseStatement.applyOrElse(CaseStatements.scala:29) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:175) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at scala.PartialFunction$OrElse.applyOrElse(PartialFunction.scala:176) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive(Actor.scala:547) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.Actor.aroundReceive$(Actor.scala:545) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.AbstractActor.aroundReceive(AbstractActor.scala:229) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.receiveMessage(ActorCell.scala:590) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.actor.ActorCell.invoke(ActorCell.scala:557) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.processMailbox(Mailbox.scala:280) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.run(Mailbox.scala:241) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at org.apache.pekko.dispatch.Mailbox.exec(Mailbox.scala:253) [flink-rpc-akka2a694021-6a60-4778-a0fe-0d2ad4672199.jar:1.20.0]
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.scan(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinPool.runWorker(Unknown Source) [?:?]
        at java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source) [?:?]
    ```
  - application cluster
    ```
    ./bin/flink run-application \
        --target kubernetes-application \
        -Dkubernetes.cluster-id=localtest-application-cluster -Dkubernetes.service-account=flink -Dkubernetes.namespace=flinkcdc -Dkubernetes.artifacts.local-upload-enabled=false  -Dkubernetes.hadoop.conf.config-map.name=flinkcdc-hive-site-cm -Dkubernetes.container.image.ref=registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle local:///opt/flink/usrlib/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar   --database.hostname 10.10.27.21 --database.port "1521" --database.username flinkcdc --database.password flinkcdc --database.dbname ORCLCDB --database.schema SOE --database.table TEST_NUMBER_TABLE --sink.metastore.uri thrift://10.10.27.26:32010 --sink.warehouse s3a://tlake-ns2/warehouse --sink.table TEST_NUMBER_TABLE4 --write.parallelism "1" --debezium.mining.strategy online_catalog
    ```
    
- flink application
```
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:08:41.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:08:41.487 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:08:46.544 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:08:46.544 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:08:46.545 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:08:51.485 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:08:51.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:08:51.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:08:51.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:08:51.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:08:51.486 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:08:56.478 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:09:01.489 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:09:01.489 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:09:01.489 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:09:01.490 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:09:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:09:06.535 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:09:11.519 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:09:11.520 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:09:11.520 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:09:11.520 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:09:11.520 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:09:11.520 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:09:16.479 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:09:16.480 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:09:16.480 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:18:22.006 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:18:22.007 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:22:03.458 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:22:03.458 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:22:03.458 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:22:03.459 [XNIO-1 task-1] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:22:32.061 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:22:32.061 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:22:32.061 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:22:32.062 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
    at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethodWithGivenArgs(AbstractAspectJAdvice.java:634)
    at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethod(AbstractAspectJAdvice.java:624)
    at org.springframework.aop.aspectj.AspectJAroundAdvice.invoke(AspectJAroundAdvice.java:72)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
    at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:763)
    at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:97)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
    at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:763)
    at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:708)
    at org.apache.streampark.console.core.controller.ApplicationController$$EnhancerBySpringCGLIB$$f36f2fb3.yarn(<generated>)
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.base/java.lang.reflect.Method.invoke(Method.java:566)
    at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
    at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:150)
    at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:117)
    at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:895)
    at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808)
    at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
    at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1072)
    at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:965)
    at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)
    at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909)
    at javax.servlet.http.HttpServlet.service(HttpServlet.java:517)
    at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)
    at javax.servlet.http.HttpServlet.service(HttpServlet.java:584)
    at io.undertow.servlet.handlers.ServletHandler.handleRequest(ServletHandler.java:74)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:129)
    at org.apache.shiro.web.servlet.ProxiedFilterChain.doFilter(ProxiedFilterChain.java:61)
    at org.apache.shiro.web.servlet.AdviceFilter.executeChain(AdviceFilter.java:108)
    at org.apache.shiro.web.servlet.AdviceFilter.doFilterInternal(AdviceFilter.java:137)
    at org.apache.shiro.web.servlet.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:154)
    at org.apache.shiro.web.servlet.ProxiedFilterChain.doFilter(ProxiedFilterChain.java:66)
    at org.apache.shiro.web.servlet.AdviceFilter.executeChain(AdviceFilter.java:108)
    at org.apache.shiro.web.servlet.AdviceFilter.doFilterInternal(AdviceFilter.java:137)
    at org.apache.shiro.web.servlet.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:154)
    at org.apache.shiro.web.servlet.ProxiedFilterChain.doFilter(ProxiedFilterChain.java:66)
    at org.apache.shiro.web.servlet.AbstractShiroFilter.executeChain(AbstractShiroFilter.java:458)
    at org.apache.shiro.web.servlet.AbstractShiroFilter$1.call(AbstractShiroFilter.java:373)
    at org.apache.shiro.subject.support.SubjectCallable.doCall(SubjectCallable.java:90)
    at org.apache.shiro.subject.support.SubjectCallable.call(SubjectCallable.java:83)
    at org.apache.shiro.subject.support.DelegatingSubject.execute(DelegatingSubject.java:387)
    at org.apache.shiro.web.servlet.AbstractShiroFilter.doFilterInternal(AbstractShiroFilter.java:370)
    at org.apache.shiro.web.servlet.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:154)
    at io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:67)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
    at org.apache.streampark.console.base.config.WebMvcConfig.doFilterInternal(WebMvcConfig.java:59)
    at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)
    at io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:67)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
    at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)
    at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)
    at io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:67)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
    at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)
    at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)
    at io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:67)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
    at org.springframework.boot.actuate.metrics.web.servlet.WebMvcMetricsFilter.doFilterInternal(WebMvcMetricsFilter.java:96)
    at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)
    at io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:67)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
    at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)
    at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:117)
    at io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:67)
    at io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
    at io.undertow.servlet.handlers.FilterHandler.handleRequest(FilterHandler.java:84)
    at io.undertow.servlet.handlers.security.ServletSecurityRoleHandler.handleRequest(ServletSecurityRoleHandler.java:62)
    at io.undertow.servlet.handlers.ServletChain$1.handleRequest(ServletChain.java:68)
    at io.undertow.servlet.handlers.ServletDispatchingHandler.handleRequest(ServletDispatchingHandler.java:36)
    at io.undertow.servlet.handlers.RedirectDirHandler.handleRequest(RedirectDirHandler.java:68)
    at io.undertow.servlet.handlers.security.SSLInformationAssociationHandler.handleRequest(SSLInformationAssociationHandler.java:117)
    at io.undertow.servlet.handlers.security.ServletAuthenticationCallHandler.handleRequest(ServletAuthenticationCallHandler.java:57)
    at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
    at io.undertow.security.handlers.AbstractConfidentialityHandler.handleRequest(AbstractConfidentialityHandler.java:46)
    at io.undertow.servlet.handlers.security.ServletConfidentialityConstraintHandler.handleRequest(ServletConfidentialityConstraintHandler.java:64)
    at io.undertow.security.handlers.AuthenticationMechanismsHandler.handleRequest(AuthenticationMechanismsHandler.java:60)
    at io.undertow.servlet.handlers.security.CachedAuthenticatedSessionHandler.handleRequest(CachedAuthenticatedSessionHandler.java:77)
    at io.undertow.security.handlers.AbstractSecurityContextAssociationHandler.handleRequest(AbstractSecurityContextAssociationHandler.java:43)
    at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
    at io.undertow.servlet.handlers.SendErrorPageHandler.handleRequest(SendErrorPageHandler.java:52)
    at io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
    at io.undertow.servlet.handlers.ServletInitialHandler.handleFirstRequest(ServletInitialHandler.java:275)
    at io.undertow.servlet.handlers.ServletInitialHandler.access$100(ServletInitialHandler.java:79)
    at io.undertow.servlet.handlers.ServletInitialHandler$2.call(ServletInitialHandler.java:134)
    at io.undertow.servlet.handlers.ServletInitialHandler$2.call(ServletInitialHandler.java:131)
    at io.undertow.servlet.core.ServletRequestContextThreadSetupAction$1.call(ServletRequestContextThreadSetupAction.java:48)
    at io.undertow.servlet.core.ContextClassLoaderSetupAction$1.call(ContextClassLoaderSetupAction.java:43)
    at io.undertow.servlet.handlers.ServletInitialHandler.dispatchRequest(ServletInitialHandler.java:255)
    at io.undertow.servlet.handlers.ServletInitialHandler.access$000(ServletInitialHandler.java:79)
    at io.undertow.servlet.handlers.ServletInitialHandler$1.handleRequest(ServletInitialHandler.java:100)
    at io.undertow.server.Connectors.executeRootHandler(Connectors.java:393)
    at io.undertow.server.HttpServerExchange$1.run(HttpServerExchange.java:852)
    at org.jboss.threads.ContextClassLoaderSavingRunnable.run(ContextClassLoaderSavingRunnable.java:35)
    at org.jboss.threads.EnhancedQueueExecutor.safeRun(EnhancedQueueExecutor.java:2019)
    at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.doRunTask(EnhancedQueueExecutor.java:1558)
    at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.run(EnhancedQueueExecutor.java:1449)
    at org.xnio.XnioWorker$WorkerThreadFactory$1$1.run(XnioWorker.java:1282)
    at java.base/java.lang.Thread.run(Thread.java:829)
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:23:10.560 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:23:10.561 [XNIO-1 task-4] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:23:37.314 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:23:37.315 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:23:37.315 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:28:36.322 [XNIO-1 task-5] DEBUG org.apache.streampark.common.util.CommandUtils - [StreamPark] Command execute:
java -classpath flink-dist-1.20.0.jar org.apache.flink.client.cli.CliFrontend --version 
22:28:37.198 [XNIO-1 task-5] INFO org.apache.streampark.common.conf.FlinkVersion - [StreamPark] /bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Version: 1.20.0, Commit ID: b1fe7b4

2025-02-19 22:28:37 | INFO  | XNIO-1 task-5 | org.apache.streampark.console.core.entity.Application:411] local appHome: /tmp/streampark/workspace/10001
2025-02-19 22:28:37 | INFO  | XNIO-1 task-5 | org.apache.streampark.console.core.service.impl.AppBuildPipeServiceImpl:381] Submit params to building pipeline : FlinkK8sApplicationBuildRequest(flinkcdc-test-application,/tmp/streampark/workspace/10001,com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI,/tmp/streampark/uploads/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar,KUBERNETES_NATIVE_APPLICATION,CUSTOM_CODE,
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------
,MavenArtifact(Set(),Set()),flinkcdc-test-application,flinkcdc,registry.tde.sktelecom.com/emergingdp/tlake/flink:registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle,K8sPodTemplates(,,),false,DockerConf(null,null,null,null),null)
22:28:37.294 [streampark-flink-buildPipeline-0] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline is launching, params=FlinkK8sApplicationBuildRequest(flinkcdc-test-application,/tmp/streampark/workspace/10001,com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI,/tmp/streampark/uploads/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar,KUBERNETES_NATIVE_APPLICATION,CUSTOM_CODE,
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------
,MavenArtifact(Set(),Set()),flinkcdc-test-application,flinkcdc,registry.tde.sktelecom.com/emergingdp/tlake/flink:registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle,K8sPodTemplates(,,),false,DockerConf(null,null,null,null),null) | appName=flinkcdc-test-application
22:28:37.295 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[1/7] running => Create building workspace | appName=flinkcdc-test-application
22:28:37.298 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] recreate building workspace: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc | appName=flinkcdc-test-application
22:28:37.298 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[1/7] success | appName=flinkcdc-test-application
22:28:37.301 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[2/7] skipped => Export kubernetes pod template | appName=flinkcdc-test-application
22:28:37.304 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[3/7] running => Build shaded flink app jar | appName=flinkcdc-test-application
22:28:37.307 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] 
start resolving dependencies...
--------------------------------------------------------------------------------
|User-declared dependencies list?


|Indirect dependencies list:


|Exclusion indirect dependencies list:
org.apache.flink:force-shading,
org.apache.flink:flink-shaded-force-shading,
com.google.code.findbugs:jsr305,
org.apache.logging.log4j:*

|Final dependencies list:

--------------------------------------------------------------------------------

22:28:37.308 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] start shaded fat-jar: /tmp/streampark/uploads/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar
22:28:37.311 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] finish build fat-jar: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc/streampark-flinkjob_flinkcdc-test-application.jar
22:28:37.312 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] output shaded flink job jar: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc/streampark-flinkjob_flinkcdc-test-application.jar | appName=flinkcdc-test-application
22:28:37.312 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[3/7] success | appName=flinkcdc-test-application
22:28:37.314 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[4/7] running => Export flink app dockerfile | appName=flinkcdc-test-application
22:28:37.321 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] output flink dockerfile: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc/Dockerfile, content: 
FROM registry.tde.sktelecom.com/emergingdp/tlake/flink:registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle
RUN mkdir -p $FLINK_HOME/usrlib
COPY lib $FLINK_HOME/lib/
COPY streampark-flinkjob_flinkcdc-test-application.jar $FLINK_HOME/usrlib/streampark-flinkjob_flinkcdc-test-application.jar
 | appName=flinkcdc-test-application
22:28:37.321 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[4/7] success | appName=flinkcdc-test-application
22:28:37.324 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[5/7] running => Pull flink app base docker image | appName=flinkcdc-test-application
22:28:38.003 [streampark-pipeline-watcher-executor-2] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[5/7] failure => Pull flink app base docker image | appName=flinkcdc-test-application
22:28:38.093 [streampark-flink-buildPipeline-0] ERROR org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline has failed. | appName=flinkcdc-test-application
java.util.concurrent.ExecutionException: com.github.dockerjava.core.exception.InvalidRepositoryNameException: Repository name "emergingdp/tlake/flink:registry.tde.sktelecom.com/emergingdp/tlake/flink" is invalid. Component: flink:registry.tde.sktelecom.com
    at java.base/java.util.concurrent.FutureTask.report(FutureTask.java:122)
    at java.base/java.util.concurrent.FutureTask.get(FutureTask.java:205)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.$anonfun$launch$1(BuildPipeline.scala:136)
    at scala.util.Try$.apply(Try.scala:213)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.launch(BuildPipeline.scala:129)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.launch$(BuildPipeline.scala:127)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.launch(FlinkK8sApplicationBuildPipeline.scala:42)
    at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
    at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
    at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
    at java.base/java.lang.Thread.run(Thread.java:829)
Caused by: com.github.dockerjava.core.exception.InvalidRepositoryNameException: Repository name "emergingdp/tlake/flink:registry.tde.sktelecom.com/emergingdp/tlake/flink" is invalid. Component: flink:registry.tde.sktelecom.com
    at com.github.dockerjava.core.NameParser.validateRepoName(NameParser.java:95)
    at com.github.dockerjava.core.NameParser.resolveRepositoryName(NameParser.java:122)
    at com.github.dockerjava.core.DefaultDockerClientConfig.effectiveAuthConfig(DefaultDockerClientConfig.java:294)
    at com.github.dockerjava.core.HackDockerClient.pullImageCmd(HackDockerClient.java:53)
    at com.github.dockerjava.core.HackDockerClient.pullImageCmd(HackDockerClient.java:26)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.$anonfun$buildProcess$14(FlinkK8sApplicationBuildPipeline.scala:147)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.$anonfun$buildProcess$14$adapted(FlinkK8sApplicationBuildPipeline.scala:137)
    at org.apache.streampark.flink.packer.docker.package$.usingDockerClient(package.scala:46)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.$anonfun$buildProcess$13(FlinkK8sApplicationBuildPipeline.scala:159)
    at scala.runtime.java8.JFunction0$mcV$sp.apply(JFunction0$mcV$sp.java:23)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.$anonfun$execStep$1(BuildPipeline.scala:102)
    at scala.util.Try$.apply(Try.scala:213)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.execStep(BuildPipeline.scala:97)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.execStep$(BuildPipeline.scala:96)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.execStep(FlinkK8sApplicationBuildPipeline.scala:42)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.buildProcess(FlinkK8sApplicationBuildPipeline.scala:159)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.buildProcess(FlinkK8sApplicationBuildPipeline.scala:42)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline$$anon$1.call(BuildPipeline.scala:134)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline$$anon$1.call(BuildPipeline.scala:133)
    ... 4 common frames omitted
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:29:04.291 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:29:04.292 [XNIO-1 task-2] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:29:20.638 [XNIO-1 task-2] DEBUG org.apache.streampark.common.util.CommandUtils - [StreamPark] Command execute:
java -classpath flink-dist-1.20.0.jar org.apache.flink.client.cli.CliFrontend --version 
22:29:21.195 [XNIO-1 task-2] INFO org.apache.streampark.common.conf.FlinkVersion - [StreamPark] /bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Version: 1.20.0, Commit ID: b1fe7b4

2025-02-19 22:29:21 | INFO  | XNIO-1 task-2 | org.apache.streampark.console.core.entity.Application:411] local appHome: /tmp/streampark/workspace/10001
2025-02-19 22:29:21 | INFO  | XNIO-1 task-2 | org.apache.streampark.console.core.service.impl.AppBuildPipeServiceImpl:381] Submit params to building pipeline : FlinkK8sApplicationBuildRequest(flinkcdc-test-application,/tmp/streampark/workspace/10001,com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI,/tmp/streampark/uploads/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar,KUBERNETES_NATIVE_APPLICATION,CUSTOM_CODE,
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------
,MavenArtifact(Set(),Set()),flinkcdc-test-application,flinkcdc,registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle,K8sPodTemplates(,,),false,DockerConf(null,null,null,null),null)
22:29:21.215 [streampark-flink-buildPipeline-0] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline is launching, params=FlinkK8sApplicationBuildRequest(flinkcdc-test-application,/tmp/streampark/workspace/10001,com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI,/tmp/streampark/uploads/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar,KUBERNETES_NATIVE_APPLICATION,CUSTOM_CODE,
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------
,MavenArtifact(Set(),Set()),flinkcdc-test-application,flinkcdc,registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle,K8sPodTemplates(,,),false,DockerConf(null,null,null,null),null) | appName=flinkcdc-test-application
22:29:21.216 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[1/7] running => Create building workspace | appName=flinkcdc-test-application
22:29:21.219 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] recreate building workspace: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc | appName=flinkcdc-test-application
22:29:21.219 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[1/7] success | appName=flinkcdc-test-application
22:29:21.221 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[2/7] skipped => Export kubernetes pod template | appName=flinkcdc-test-application
22:29:21.222 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[3/7] running => Build shaded flink app jar | appName=flinkcdc-test-application
22:29:21.225 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] 
start resolving dependencies...
--------------------------------------------------------------------------------
|User-declared dependencies list?


|Indirect dependencies list:


|Exclusion indirect dependencies list:
org.apache.flink:force-shading,
org.apache.flink:flink-shaded-force-shading,
com.google.code.findbugs:jsr305,
org.apache.logging.log4j:*

|Final dependencies list:

--------------------------------------------------------------------------------

22:29:21.225 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] start shaded fat-jar: /tmp/streampark/uploads/flinkcdc-oracle-0.1-OracleToIcebergByDataStreamAPI.jar
22:29:21.229 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] finish build fat-jar: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc/streampark-flinkjob_flinkcdc-test-application.jar
22:29:21.229 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] output shaded flink job jar: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc/streampark-flinkjob_flinkcdc-test-application.jar | appName=flinkcdc-test-application
22:29:21.229 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[3/7] success | appName=flinkcdc-test-application
22:29:21.287 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[4/7] running => Export flink app dockerfile | appName=flinkcdc-test-application
22:29:21.290 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] output flink dockerfile: /tmp/streampark/workspace/10001/flinkcdc-test-application@flinkcdc/Dockerfile, content: 
FROM registry.tde.sktelecom.com/emergingdp/tlake/flink:1.20.0_scala_2.12_java11-hadoop3.2.4-hive3.1.3-iceberg1.7.1-s3-oracle
RUN mkdir -p $FLINK_HOME/usrlib
COPY lib $FLINK_HOME/lib/
COPY streampark-flinkjob_flinkcdc-test-application.jar $FLINK_HOME/usrlib/streampark-flinkjob_flinkcdc-test-application.jar
 | appName=flinkcdc-test-application
22:29:21.290 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[4/7] success | appName=flinkcdc-test-application
22:29:21.292 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[5/7] running => Pull flink app base docker image | appName=flinkcdc-test-application
2025-02-19 22:29:21 | ERROR | docker-java-stream-33457655 | com.github.dockerjava.api.async.ResultCallbackTemplate:52] Error during callback
java.lang.RuntimeException: org.apache.hc.client5.http.HttpHostConnectException: Connect to http://localhost:2375 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refused (Connection refused)
    at com.github.dockerjava.httpclient5.ApacheDockerHttpClientImpl.execute(ApacheDockerHttpClientImpl.java:195)
    at com.github.dockerjava.httpclient5.ApacheDockerHttpClient.execute(ApacheDockerHttpClient.java:9)
    at com.github.dockerjava.core.DefaultInvocationBuilder.execute(DefaultInvocationBuilder.java:228)
    at com.github.dockerjava.core.DefaultInvocationBuilder.lambda$executeAndStream$1(DefaultInvocationBuilder.java:269)
    at java.base/java.lang.Thread.run(Thread.java:829)
Caused by: org.apache.hc.client5.http.HttpHostConnectException: Connect to http://localhost:2375 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refused (Connection refused)
    at java.base/java.net.PlainSocketImpl.socketConnect(Native Method)
    at java.base/java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:412)
    at java.base/java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:255)
    at java.base/java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:237)
    at java.base/java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
    at java.base/java.net.Socket.connect(Socket.java:609)
    at org.apache.hc.client5.http.socket.PlainConnectionSocketFactory$1.run(PlainConnectionSocketFactory.java:87)
    at java.base/java.security.AccessController.doPrivileged(Native Method)
    at org.apache.hc.client5.http.socket.PlainConnectionSocketFactory.connectSocket(PlainConnectionSocketFactory.java:84)
    at org.apache.hc.client5.http.impl.io.DefaultHttpClientConnectionOperator.connect(DefaultHttpClientConnectionOperator.java:148)
    at org.apache.hc.client5.http.impl.io.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:396)
    at org.apache.hc.client5.http.impl.classic.InternalExecRuntime.connectEndpoint(InternalExecRuntime.java:158)
    at org.apache.hc.client5.http.impl.classic.InternalExecRuntime.connectEndpoint(InternalExecRuntime.java:168)
    at org.apache.hc.client5.http.impl.classic.ConnectExec.execute(ConnectExec.java:136)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.ProtocolExec.execute(ProtocolExec.java:190)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.HttpRequestRetryExec.execute(HttpRequestRetryExec.java:96)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.ContentCompressionExec.execute(ContentCompressionExec.java:133)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.RedirectExec.execute(RedirectExec.java:115)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.InternalHttpClient.doExecute(InternalHttpClient.java:170)
    at org.apache.hc.client5.http.impl.classic.CloseableHttpClient.execute(CloseableHttpClient.java:67)
    at com.github.dockerjava.httpclient5.ApacheDockerHttpClientImpl.execute(ApacheDockerHttpClientImpl.java:191)
    ... 4 common frames omitted
22:29:21.501 [streampark-pipeline-watcher-executor-3] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[5/7] failure => Pull flink app base docker image | appName=flinkcdc-test-application
22:29:21.504 [streampark-flink-buildPipeline-0] ERROR org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline - [StreamPark] [streampark-packer] building pipeline has failed. | appName=flinkcdc-test-application
java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.apache.hc.client5.http.HttpHostConnectException: Connect to http://localhost:2375 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refused (Connection refused)
    at java.base/java.util.concurrent.FutureTask.report(FutureTask.java:122)
    at java.base/java.util.concurrent.FutureTask.get(FutureTask.java:205)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.$anonfun$launch$1(BuildPipeline.scala:136)
    at scala.util.Try$.apply(Try.scala:213)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.launch(BuildPipeline.scala:129)
    at org.apache.streampark.flink.packer.pipeline.BuildPipeline.launch$(BuildPipeline.scala:127)
    at org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sApplicationBuildPipeline.launch(FlinkK8sApplicationBuildPipeline.scala:42)
    at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
    at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
    at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
    at java.base/java.lang.Thread.run(Thread.java:829)
Caused by: java.lang.RuntimeException: org.apache.hc.client5.http.HttpHostConnectException: Connect to http://localhost:2375 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refused (Connection refused)
    at com.github.dockerjava.httpclient5.ApacheDockerHttpClientImpl.execute(ApacheDockerHttpClientImpl.java:195)
    at com.github.dockerjava.httpclient5.ApacheDockerHttpClient.execute(ApacheDockerHttpClient.java:9)
    at com.github.dockerjava.core.DefaultInvocationBuilder.execute(DefaultInvocationBuilder.java:228)
    at com.github.dockerjava.core.DefaultInvocationBuilder.lambda$executeAndStream$1(DefaultInvocationBuilder.java:269)
    ... 1 common frames omitted
Caused by: org.apache.hc.client5.http.HttpHostConnectException: Connect to http://localhost:2375 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refused (Connection refused)
    at java.base/java.net.PlainSocketImpl.socketConnect(Native Method)
    at java.base/java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:412)
    at java.base/java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:255)
    at java.base/java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:237)
    at java.base/java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
    at java.base/java.net.Socket.connect(Socket.java:609)
    at org.apache.hc.client5.http.socket.PlainConnectionSocketFactory$1.run(PlainConnectionSocketFactory.java:87)
    at java.base/java.security.AccessController.doPrivileged(Native Method)
    at org.apache.hc.client5.http.socket.PlainConnectionSocketFactory.connectSocket(PlainConnectionSocketFactory.java:84)
    at org.apache.hc.client5.http.impl.io.DefaultHttpClientConnectionOperator.connect(DefaultHttpClientConnectionOperator.java:148)
    at org.apache.hc.client5.http.impl.io.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:396)
    at org.apache.hc.client5.http.impl.classic.InternalExecRuntime.connectEndpoint(InternalExecRuntime.java:158)
    at org.apache.hc.client5.http.impl.classic.InternalExecRuntime.connectEndpoint(InternalExecRuntime.java:168)
    at org.apache.hc.client5.http.impl.classic.ConnectExec.execute(ConnectExec.java:136)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.ProtocolExec.execute(ProtocolExec.java:190)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.HttpRequestRetryExec.execute(HttpRequestRetryExec.java:96)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.ContentCompressionExec.execute(ContentCompressionExec.java:133)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement$1.proceed(ExecChainElement.java:57)
    at org.apache.hc.client5.http.impl.classic.RedirectExec.execute(RedirectExec.java:115)
    at org.apache.hc.client5.http.impl.classic.ExecChainElement.execute(ExecChainElement.java:51)
    at org.apache.hc.client5.http.impl.classic.InternalHttpClient.doExecute(InternalHttpClient.java:170)
    at org.apache.hc.client5.http.impl.classic.CloseableHttpClient.execute(CloseableHttpClient.java:67)
    at com.github.dockerjava.httpclient5.ApacheDockerHttpClientImpl.execute(ApacheDockerHttpClientImpl.java:191)
    ... 4 common frames omitted

```


- flink session
```
    at java.base/java.net.URLClassLoader.findClass(URLClassLoader.java:476)
    at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:594)
    at org.apache.flink.util.FlinkUserCodeClassLoader.loadClassWithoutExceptionHandling(FlinkUserCodeClassLoader.java:67)
    at org.apache.flink.util.ChildFirstClassLoader.loadClassWithoutExceptionHandling(ChildFirstClassLoader.java:65)
    at org.apache.flink.util.FlinkUserCodeClassLoader.loadClass(FlinkUserCodeClassLoader.java:51)
    at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:527)
    ... 41 more
2025-02-19 18:00:02 | INFO  | ForkJoinPool.commonPool-worker-115 | org.apache.streampark.console.core.service.impl.ApplicationServiceImpl:1696]  start exception : java.util.concurrent.CompletionException: java.lang.reflect.InvocationTargetException
18:00:02.526 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
18:00:02.527 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
18:00:02.528 [ForkJoinPool.commonPool-worker-115] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
21:57:13.095 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
21:57:13.096 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
21:57:13.097 [XNIO-1 task-5] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
21:57:42.114 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
21:57:42.114 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
21:57:42.115 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
21:57:42.116 [XNIO-1 task-6] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:00:53.478 [XNIO-1 task-4] DEBUG org.apache.streampark.common.util.CommandUtils - [StreamPark] Command execute:
java -classpath flink-dist-1.20.0.jar org.apache.flink.client.cli.CliFrontend --version 
22:00:54.088 [XNIO-1 task-4] INFO org.apache.streampark.common.conf.FlinkVersion - [StreamPark] /bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Version: 1.20.0, Commit ID: b1fe7b4

2025-02-19 22:00:54 | INFO  | XNIO-1 task-4 | org.apache.streampark.console.core.entity.Application:411] local appHome: /tmp/streampark/workspace/10000
2025-02-19 22:00:54 | INFO  | XNIO-1 task-4 | org.apache.streampark.console.core.service.impl.AppBuildPipeServiceImpl:356] Submit params to building pipeline : FlinkK8sSessionBuildRequest(flinkcdc-test,/tmp/streampark/workspace/10000,com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI,/tmp/streampark/uploads/flinkcdc-oracle-0.1.jar,KUBERNETES_NATIVE_SESSION,CUSTOM_CODE,
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------
,MavenArtifact(Set(),Set()),tlake-ns2-session-cluster,flinkcdc)
22:00:54.752 [streampark-flink-buildPipeline-0] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] building pipeline is launching, params=FlinkK8sSessionBuildRequest(flinkcdc-test,/tmp/streampark/workspace/10000,com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI,/tmp/streampark/uploads/flinkcdc-oracle-0.1.jar,KUBERNETES_NATIVE_SESSION,CUSTOM_CODE,
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------
,MavenArtifact(Set(),Set()),tlake-ns2-session-cluster,flinkcdc) | appName=flinkcdc-test
22:00:54.753 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[1/2] running => Create building workspace | appName=flinkcdc-test
22:00:54.796 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] recreate building workspace: /tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc | appName=flinkcdc-test
22:00:54.796 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[1/2] success | appName=flinkcdc-test
22:00:54.800 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[2/2] running => Build shaded flink app jar | appName=flinkcdc-test
22:00:54.807 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] 
start resolving dependencies...
--------------------------------------------------------------------------------
|User-declared dependencies list?


|Indirect dependencies list:


|Exclusion indirect dependencies list:
org.apache.flink:force-shading,
org.apache.flink:flink-shaded-force-shading,
com.google.code.findbugs:jsr305,
org.apache.logging.log4j:*

|Final dependencies list:

--------------------------------------------------------------------------------

22:00:54.920 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] start shaded fat-jar: /tmp/streampark/uploads/flinkcdc-oracle-0.1.jar



22:02:22.192 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.maven.MavenTool - [StreamPark] finish build fat-jar: /tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc/streampark-flinkjob_flinkcdc-test.jar
22:02:22.192 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] output shaded flink job jar: /tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc/streampark-flinkjob_flinkcdc-test.jar | appName=flinkcdc-test
22:02:22.192 [streampark-pipeline-watcher-executor-1] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] building pipeline step[2/2] success | appName=flinkcdc-test
22:02:22.199 [streampark-flink-buildPipeline-0] INFO org.apache.streampark.flink.packer.pipeline.impl.FlinkK8sSessionBuildPipeline - [StreamPark] [streampark-packer] building pipeline has finished successfully. | appName=flinkcdc-test
22:02:29.022 [streampark-flink-app-bootstrap-2] DEBUG org.apache.streampark.common.util.CommandUtils - [StreamPark] Command execute:
java -classpath flink-dist-1.20.0.jar org.apache.flink.client.cli.CliFrontend --version 
22:02:29.698 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.common.conf.FlinkVersion - [StreamPark] /bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Version: 1.20.0, Commit ID: b1fe7b4

22:02:29.698 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.proxy.FlinkShimsProxy - [StreamPark] add flink shims urls classloader,flink version: 
----------------------------------------- flink version -----------------------------------
     flinkHome    : /opt/flink/flink-1.20.0
     distJarName  : flink-dist-1.20.0.jar
     flinkVersion : 1.20.0
     majorVersion : 1.20
     scalaVersion : 2.12
     shimsVersion : streampark-flink-shims_flink-1.20
-------------------------------------------------------------------------------------------

--database.username
flinkcdc
--database.password
flinkcdc
--database.dbname
ORCLCDB
--database.schema
SOE
--database.table
TEST_NUMBER_TABLE
--sink.metastore.uri
thrift://10.10.27.26:32010
--sink.warehouse
s3a://tlake-ns2/warehouse
--write.parallelism
"1"
--debezium.mining.strategy
online_catalog
    appConf          : json://{"$internal.application.main":"com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI"}
    flinkBuildResult : { workspacePath: /tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc, shadedJarPath: /tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc/streampark-flinkjob_flinkcdc-test.jar, pass: true }
-------------------------------------------------------------------------------------------

2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:160] Using standard YAML parser to load flink configuration file from /opt/flink/flink-1.20.0/conf/config.yaml.
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.memory.process.size, 1728m
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.bind-host, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.execution.failover-strategy, region
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.rpc.address, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.memory.process.size, 1600m
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.rpc.port, 6123
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: rest.bind-address, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.bind-host, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.host, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: parallelism.default, 1
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.numberOfTaskSlots, 1
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: rest.address, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: env.java.opts.all, --add-exports=java.base/sun.net.util=ALL-UNNAMED --add-exports=java.rmi/sun.rmi.registry=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED --add-exports=java.security.jgss/sun.security.krb5=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.nio=ALL-UNNAMED --add-opens=java.base/sun.nio.ch=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.text=ALL-UNNAMED --add-opens=java.base/java.time=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.atomic=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.locks=ALL-UNNAMED
22:02:29.897 [streampark-flink-app-bootstrap-2] WARN org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] param:$internal.application.main is error,skip it.
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  kubernetes.container.image.pull-policy :Always
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  s3.access-key :minioadmin
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  s3.connection.ssl.enabled :false
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  s3.secret-key :minioadmin
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  s3.endpoint :http://10.10.27.23:9000
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  s3.path.style.access :true
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit application dynamicProperties:  classloader.resolve-order :parent-first
22:02:29.897 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - cliArgs: -t kubernetes-session -Dkubernetes.container.image.pull-policy=Always -Ds3.access-key=minioadmin -Ds3.connection.ssl.enabled=false -Ds3.secret-key=minioadmin -Ds3.endpoint=http://10.10.27.23:9000 -Ds3.path.style.access=true -Dclassloader.resolve-order=parent-first
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:160] Using standard YAML parser to load flink configuration file from /opt/flink/flink-1.20.0/conf/config.yaml.
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.memory.process.size, 1728m
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.bind-host, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.execution.failover-strategy, region
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.rpc.address, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.memory.process.size, 1600m
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.rpc.port, 6123
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: rest.bind-address, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: jobmanager.bind-host, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.host, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: parallelism.default, 1
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: taskmanager.numberOfTaskSlots, 1
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: rest.address, localhost
2025-02-19 22:02:29 | INFO  | streampark-flink-app-bootstrap-2 | org.apache.flink.configuration.GlobalConfiguration:186] Loading configuration property: env.java.opts.all, --add-exports=java.base/sun.net.util=ALL-UNNAMED --add-exports=java.rmi/sun.rmi.registry=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED --add-exports=java.security.jgss/sun.security.krb5=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.nio=ALL-UNNAMED --add-opens=java.base/sun.nio.ch=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.text=ALL-UNNAMED --add-opens=java.base/java.time=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.atomic=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.locks=ALL-UNNAMED
22:02:29.901 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] Custom commandline: [org.apache.flink.client.cli.GenericCLI@5fab3b23, org.apache.flink.yarn.cli.FlinkYarnSessionCli@2a6f0bf2, org.apache.flink.client.cli.DefaultCLI@3f83169b]
22:02:29.901 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] Checking custom commandline org.apache.flink.client.cli.GenericCLI@5fab3b23, isActive: true
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  jobmanager.execution.failover-strategy: region
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  jobmanager.rpc.address: localhost
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  execution.checkpointing.num-retained: 1
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  s3.connection.ssl.enabled: false
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  jobmanager.bind-host: localhost
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  s3.secret-key: minioadmin
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  s3.endpoint: http://10.10.27.23:9000
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  $internal.application.program-args: [--database.hostname, 10.10.27.21, --database.port, '1521', --database.username, flinkcdc, --database.password, flinkcdc, --database.dbname, ORCLCDB, --database.schema, SOE, --database.table, TEST_NUMBER_TABLE, --sink.metastore.uri, 'thrift://10.10.27.26:32010', --sink.warehouse, 's3a://tlake-ns2/warehouse', --write.parallelism, '1', --debezium.mining.strategy, online_catalog]
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  taskmanager.host: localhost
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  parallelism.default: 1
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  taskmanager.numberOfTaskSlots: 1
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  pipeline.classpaths: ]
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  env.java.opts.all: --add-exports=java.base/sun.net.util=ALL-UNNAMED --add-exports=java.rmi/sun.rmi.registry=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED --add-exports=java.security.jgss/sun.security.krb5=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.nio=ALL-UNNAMED --add-opens=java.base/sun.nio.ch=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.text=ALL-UNNAMED --add-opens=java.base/java.time=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.atomic=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.locks=ALL-UNNAMED
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  $internal.application.main: com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  taskmanager.memory.process.size: 1728m
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  taskmanager.bind-host: localhost
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  pipeline.name: flinkcdc-test
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  classloader.resolve-order: parent-first
22:02:29.904 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  execution.target: kubernetes-session
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  jobmanager.memory.process.size: 1600m
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  jobmanager.rpc.port: 6123
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  rest.bind-address: localhost
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  s3.access-key: minioadmin
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  execution.state-recovery.claim-mode: NO_CLAIM
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  kubernetes.container.image.pull-policy: Always
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  execution.attached: true
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  $internal.pipeline.job-id: ba2c61f4066b01b5713e04d8c2d2b55b
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  execution.shutdown-on-attached-exit: false
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  pipeline.jars: ['file:/tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc/streampark-flinkjob_flinkcdc-test.jar']
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  rest.address: localhost
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  execution.state-recovery.ignore-unclaimed-state: false
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  $internal.deployment.config-dir: /opt/flink/flink-1.20.0/conf
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] flinkConfig:  s3.path.style.access: true
22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] 
------------------------------------------------------------------
Effective submit configuration: {jobmanager.execution.failover-strategy=region, jobmanager.rpc.address=localhost, execution.checkpointing.num-retained=1, kubernetes.jobmanager.labels={jobId=ba2c61f4066b01b5713e04d8c2d2b55b}, kubernetes.cluster-id=tlake-ns2-session-cluster, s3.connection.ssl.enabled=false, jobmanager.bind-host=localhost, s3.secret-key=******, s3.endpoint=http://10.10.27.23:9000, $internal.application.program-args=[--database.hostname, 10.10.27.21, --database.port, 1521, --database.username, flinkcdc, --database.password, flinkcdc, --database.dbname, ORCLCDB, --database.schema, SOE, --database.table, TEST_NUMBER_TABLE, --sink.metastore.uri, thrift://10.10.27.26:32010, --sink.warehouse, s3a://tlake-ns2/warehouse, --write.parallelism, 1, --debezium.mining.strategy, online_catalog], taskmanager.host=localhost, parallelism.default=1, kubernetes.namespace=flinkcdc, taskmanager.numberOfTaskSlots=1, pipeline.classpaths=], kubernetes.rest-service.exposed.type=ClusterIP, env.java.opts.all=--add-exports=java.base/sun.net.util=ALL-UNNAMED --add-exports=java.rmi/sun.rmi.registry=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED --add-exports=jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED --add-exports=java.security.jgss/sun.security.krb5=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.nio=ALL-UNNAMED --add-opens=java.base/sun.nio.ch=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED --add-opens=java.base/java.text=ALL-UNNAMED --add-opens=java.base/java.time=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.atomic=ALL-UNNAMED --add-opens=java.base/java.util.concurrent.locks=ALL-UNNAMED, $internal.application.main=com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI, taskmanager.memory.process.size=1728m, taskmanager.bind-host=localhost, pipeline.name=flinkcdc-test, classloader.resolve-order=parent-first, execution.target=kubernetes-session, jobmanager.memory.process.size=1600m, jobmanager.rpc.port=6123, rest.bind-address=localhost, s3.access-key=minioadmin, execution.state-recovery.claim-mode=NO_CLAIM, kubernetes.container.image.pull-policy=Always, execution.attached=true, $internal.pipeline.job-id=ba2c61f4066b01b5713e04d8c2d2b55b, execution.shutdown-on-attached-exit=false, pipeline.jars=[file:/tmp/streampark/workspace/10000/tlake-ns2-session-cluster@flinkcdc/streampark-flinkjob_flinkcdc-test.jar], kubernetes.taskmanager.labels={jobId=ba2c61f4066b01b5713e04d8c2d2b55b}, rest.address=localhost, execution.state-recovery.ignore-unclaimed-state=false, $internal.deployment.config-dir=/opt/flink/flink-1.20.0/conf, s3.path.style.access=true}
------------------------------------------------------------------

22:02:29.905 [streampark-flink-app-bootstrap-2] INFO org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] [flink-submit] Submit job with JobGraph Plan.
2025-02-19 22:02:30 | INFO  | streampark-flink-app-bootstrap-2 | com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI:37] Parameters ->
 dbType: pdb
 hostname: 10.10.27.21
 port: 1521
 racNodes: 
 username: flinkcdc
 password: flinkcdc
 dbName: ORCLCDB
 pdbName: 
 heartbeatIntervalMs: null
 heartbeatActionQuery: null
 schemaName: SOE
 tableName: TEST_NUMBER_TABLE
 sinkMetastoreUri: thrift://10.10.27.26:32010
 sinkWarehouse: s3a://tlake-ns2/warehouse
 sinkCatalogName: iceberg_hive
 sinkSchemaName: SOE
 sinkTableName: TEST_NUMBER_TABLE
 sinkTablePartitionColumns: null
 sinkTableEqualityFieldColumns: null
 checkpointInterval: 10000
 jdbcUrl: jdbc:oracle:thin:@10.10.27.21:1521:ORCLCDB
 jdbcPdbUrl: jdbc:oracle:thin:@10.10.27.21:1521:ORCLCDB
 splitSize: 8096
 fetchSize: 1024
 writeParallelism: 1
 runningMode: SourceFunction
 logMingStrategy: online_catalog
 maxBatchSize: 2048
 maxQueueSize: 8192
 logMingViewFetchSize: 10000
 logMingBatchSizeDefault: 20000
 logMingBatchSizeMax: 100000
 logMiningUsernameExcludeList: FLINKCDC
22:02:30.698 [streampark-flink-app-bootstrap-2] ERROR org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient - [StreamPark] submit flink job fail in KUBERNETES_NATIVE_SESSION mode
org.apache.flink.client.program.ProgramInvocationException: The main method caused an error: java.sql.SQLException: No suitable driver found for jdbc:oracle:thin:@10.10.27.21:1521:ORCLCDB
    at org.apache.flink.client.program.PackagedProgram.callMainMethod(PackagedProgram.java:373)
    at org.apache.flink.client.program.PackagedProgram.invokeInteractiveModeForExecution(PackagedProgram.java:223)
    at org.apache.flink.client.program.PackagedProgramUtils.getPipelineFromProgram(PackagedProgramUtils.java:158)
    at org.apache.flink.client.program.PackagedProgramUtils.createJobGraph(PackagedProgramUtils.java:82)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.getJobGraph(FlinkClientTrait.scala:258)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.getJobGraph$(FlinkClientTrait.scala:237)
    at org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient$.jobGraphSubmit(KubernetesNativeSessionClient.scala:102)
    at org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient$.$anonfun$doSubmit$2(KubernetesNativeSessionClient.scala:57)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.$anonfun$trySubmit$1(FlinkClientTrait.scala:213)
    at scala.util.Try$.apply(Try.scala:209)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.trySubmit(FlinkClientTrait.scala:211)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.trySubmit$(FlinkClientTrait.scala:207)
    at org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient$.doSubmit(KubernetesNativeSessionClient.scala:58)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.$anonfun$submit$4(FlinkClientTrait.scala:83)
    at scala.util.Try$.apply(Try.scala:209)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.submit(FlinkClientTrait.scala:83)
    at org.apache.streampark.flink.client.trait.FlinkClientTrait.submit$(FlinkClientTrait.scala:58)
    at org.apache.streampark.flink.client.impl.KubernetesNativeSessionClient$.submit(KubernetesNativeSessionClient.scala:45)
    at org.apache.streampark.flink.client.FlinkClientEntrypoint$.submit(FlinkClientEntrypoint.scala:40)
    at org.apache.streampark.flink.client.FlinkClientEntrypoint.submit(FlinkClientEntrypoint.scala)
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.base/java.lang.reflect.Method.invoke(Method.java:566)
    at org.apache.streampark.flink.client.FlinkClient$.$anonfun$proxy$1(FlinkClient.scala:87)
    at org.apache.streampark.flink.proxy.FlinkShimsProxy$.$anonfun$proxy$1(FlinkShimsProxy.scala:60)
    at org.apache.streampark.common.util.ClassLoaderUtils$.runAsClassLoader(ClassLoaderUtils.scala:38)
    at org.apache.streampark.flink.proxy.FlinkShimsProxy$.proxy(FlinkShimsProxy.scala:60)
    at org.apache.streampark.flink.client.FlinkClient$.proxy(FlinkClient.scala:82)
    at org.apache.streampark.flink.client.FlinkClient$.submit(FlinkClient.scala:53)
    at org.apache.streampark.flink.client.FlinkClient.submit(FlinkClient.scala)
    at org.apache.streampark.console.core.service.impl.ApplicationServiceImpl.lambda$start$8(ApplicationServiceImpl.java:1685)
    at java.base/java.util.concurrent.CompletableFuture$AsyncSupply.run(CompletableFuture.java:1700)
    at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
    at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
    at java.base/java.lang.Thread.run(Thread.java:829)
Caused by: java.lang.RuntimeException: java.sql.SQLException: No suitable driver found for jdbc:oracle:thin:@10.10.27.21:1521:ORCLCDB
    at com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI.main(OracleToIcebergByDataStreamAPI.java:92)
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.base/java.lang.reflect.Method.invoke(Method.java:566)
    at org.apache.flink.client.program.PackagedProgram.callMainMethod(PackagedProgram.java:356)
    ... 35 more
Caused by: java.sql.SQLException: No suitable driver found for jdbc:oracle:thin:@10.10.27.21:1521:ORCLCDB
    at java.sql/java.sql.DriverManager.getConnection(DriverManager.java:702)
    at java.sql/java.sql.DriverManager.getConnection(DriverManager.java:228)
    at com.sktelecom.aidata.flinkcdc.schema.SchemaManager.scrapeSourceSinkSchemaPair(SchemaManager.java:73)
    at com.sktelecom.aidata.flinkcdc.schema.SchemaManager.<init>(SchemaManager.java:36)
    at com.sktelecom.aidata.flinkcdc.job.OracleToIcebergByDataStreamAPI.main(OracleToIcebergByDataStreamAPI.java:84)
    ... 40 more
2025-02-19 22:02:30 | INFO  | ForkJoinPool.commonPool-worker-229 | org.apache.streampark.console.core.service.impl.ApplicationServiceImpl:1696]  start exception : java.util.concurrent.CompletionException: java.lang.reflect.InvocationTargetException
22:02:30.889 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:02:30.889 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:02:30.889 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:02:30.890 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:02:30.891 [ForkJoinPool.commonPool-worker-229] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:03:56.485 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:03:56.485 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:03:56.485 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:03:56.486 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:04:01.545 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:04:01.545 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:04:01.545 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:04:01.546 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:04:06.533 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:04:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:04:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:04:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:04:06.534 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:04:11.538 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:04:11.539 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:04:11.539 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:04:11.539 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 21 : env:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 22 :   java:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 23 :     opts:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 30 : jobmanager:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 39 :   rpc:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 51 :   memory:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 52 :     process:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 56 :   execution:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 62 : taskmanager:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 83 :   memory:
22:04:16.544 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 84 :     process:
22:04:16.545 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 92 : parallelism:
22:04:16.545 [XNIO-1 task-3] WARN org.apache.streampark.common.util.PropertiesUtils - [StreamPark] Error while trying to split key and value in configuration. 168 : rest:

```
