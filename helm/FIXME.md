- [Native Kubernetes](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/deployment/resource-providers/native_kubernetes/)
- [Kubernetes Configuration](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/deployment/config/#kubernetes)
- test
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
