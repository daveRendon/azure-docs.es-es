---
title: Puertos utilizados por HDInsight | Microsoft Docs
description: Una lista de puertos utilizados por los servicios Hadoop que se ejecutan en HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd14aed9-ec25-4bb3-a20c-e29562735a7d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b65e9e6b196965a7df1e4979219117fb87cedbd7


---
# <a name="ports-and-uris-used-by-hdinsight"></a>Puertos e identificadores URI usados en HDInsight
En este documento se proporciona una lista de puertos que se usan con los servicios de Hadoop que se ejecutan en clústeres de HDInsight basados en Linux. También se proporciona información sobre los puertos utilizados para conectarse al clúster mediante SSH.

## <a name="public-ports-vs-non-public-ports"></a>Puertos públicos frente a puertos no públicos
Los clústeres de HDInsight basados en Linux solo exponen tres puertos públicamente en Internet: 22, 23 y 443. Estos puertos se usan para el acceso seguro al clúster mediante SSH y a los servicios expuestos a través del protocolo HTTPS seguro.

Internamente, HDInsight se implementa mediante varias máquinas virtuales de Azure (los nodos del clúster) que se ejecutan en una red virtual de Azure. Desde dentro de la red virtual, puede acceder a los puertos no expuestos a través de Internet. Por ejemplo, si se conecta a uno de los nodos principales con SSH, desde él puede acceder directamente a los servicios que se ejecutan en los nodos del clúster.

> [!IMPORTANT]
> Al crear un clúster de HDInsight, si no especifica una red virtual de Azure como opción de configuración, se crea una; sin embargo, no puede unir otros equipos (por ejemplo, otras máquinas virtuales de Azure o el equipo de desarrollo de cliente) a esta red virtual creada automáticamente. 
> 
> 

Para unir equipos adicionales a la red virtual, debe crear primero la red virtual y luego especificarla al crear el clúster de HDInsight. Para más información, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Puertos públicos
Todos los nodos de un clúster de HDInsight se encuentran en una red virtual de Azure y no son accesibles directamente desde Internet. Una puerta de enlace pública proporciona acceso desde Internet a los puertos siguientes, que son comunes a todos los tipos de clúster de HDInsight.

| Servicio | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |Conecta los clientes a sshd en el nodo primario principal. Consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd |22 |SSH |Conecta los clientes a sshd en el nodo de borde (solo HDInsight Premium). Consulte [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md) |
| sshd |23 |SSH |Conecta los clientes a sshd en el nodo primario secundario. Consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari |443 |HTTPS |Interfaz de usuario web de Ambari. Consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API de REST de Ambari. Consulte [Administración de clústeres de HDInsight con la API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API de REST de HCatalog. Consulte [Uso de Hive con Curl](hdinsight-hadoop-use-pig-curl.md), [Uso de Pig con Curl](hdinsight-hadoop-use-pig-curl.md) y [Uso de MapReduce con Curl](hdinsight-hadoop-use-mapreduce-curl.md). |
| HiveServer2 |443 |ODBC |Conecta a Hive mediante ODBC. Consulte [Conexión de Excel en HDInsight con el controlador ODBC de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta a Hive mediante JDBC. Consulte [Conexión a Hive en HDInsight de Azure con el controlador JDBC de Hive](hdinsight-connect-hive-jdbc-driver.md) |

Las siguientes opciones están disponibles para determinados tipos de clúster:

| Servicio | Port | Protocol | Tipo de clúster | Description |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API de REST de HBase. Consulte [Introducción al uso de HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API de REST de Spark. Consulte [Envío de trabajos de Spark remotamente a un clúster de Apache Spark en HDInsight Linux mediante Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Storm |443 |HTTPS |Storm |La interfaz de usuario web de Storm. Consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autenticación
Todos los servicios expuestos públicamente en Internet se deben autenticar:

| Port | Credenciales |
| --- | --- |
| 22 o 23 |Las credenciales de usuario de SSH especificadas durante la creación del clúster. |
| 443 |El nombre de inicio de sesión (predeterminado: admin) y la contraseña que se establecieron durante la creación del clúster. |

## <a name="non-public-ports"></a>Puertos no públicos
> [!NOTE]
> Algunos servicios solo están disponibles en determinados tipos de clúster. Por ejemplo, HBase solo está disponible en los tipos de clúster de HBase.
> 
> 

### <a name="hdfs-ports"></a>Puertos HDFS
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Interfaz de usuario web de NameNode |Nodos principales |30070 |HTTPS |Interfaz de usuario web para ver el estado actual |
| Servicio de metadatos de NameNode |Nodos principales |8020 |IPC |Metadatos del sistema de archivos |
| DataNode |Todos los nodos de trabajo |30075 |HTTPS |Interfaz de usuario web para ver el estado, los registros, etc. |
| DataNode |Todos los nodos de trabajo |30010 |&nbsp; |Transferencia de datos |
| DataNode |Todos los nodos de trabajo |30020 |IPC |Operaciones de metadatos |
| NameNode secundario |Nodos principales |50090 |HTTP |Punto de control para metadatos de NameNode |

### <a name="yarn-ports"></a>Puertos de YARN
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Interfaz de usuario web de Resource Manager |Nodos principales |8088 |HTTP |Interfaz de usuario web para Resource Manager |
| Interfaz de usuario web de Resource Manager |Nodos principales |8090 |HTTPS |Interfaz de usuario web para Resource Manager |
| Interfaz de administración de Resource Manager |Nodos principales |8141 |IPC |Para envíos de aplicaciones (Hive, servidor de Hive, Pig, etc.) |
| Programador de Resource Manager |Nodos principales |8030 |HTTP |Interfaz administrativa |
| Interfaz de aplicación de Resource Manager |Nodos principales |8050 |HTTP |Dirección de la interfaz del administrador de aplicaciones |
| NodeManager |Todos los nodos de trabajo |30050 |&nbsp; |La dirección del administrador de contenedores |
| Interfaz de usuario web de NodeManager |Todos los nodos de trabajo |30060 |HTTP |Interfaz de Resource Manager |
| Dirección de escala de tiempo |Nodos principales |10200 |RPC |El servicio RPC del servicio de escala de tiempo. |
| Interfaz de usuario web de escala de tiempo |Nodos principales |8181 |HTTP |La interfaz de usuario web del servicio de escala de tiempo |

### <a name="hive-ports"></a>Puertos de Hive
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nodos principales |10001 |Thrift |Servicio para conectarse mediante programación a Hive (Thrift/JDBC) |
| HiveServer |Nodos principales |10000 |Thrift |Servicio para conectarse mediante programación a Hive (Thrift/JDBC) |
| Tienda de metadatos Hive |Nodos principales |9083 |Thrift |Servicio para conectarse mediante programación a metadatos de Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Puertos de WebHCat
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Servidor de WebHCat |Nodos principales |30111 |HTTP |Web API encima de HCatalog y otros servicios de Hadoop |

### <a name="mapreduce-ports"></a>Puertos de MapReduce
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Historial de trabajos |Nodos principales |19888 |HTTP |Interfaz de usuario web del historial de trabajos de MapReduce |
| Historial de trabajos |Nodos principales |10020 |&nbsp; |Servidor de historial de trabajos de MapReduce |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfiere las salidas de mapa intermedias a los reductores solicitantes |

### <a name="oozie"></a>Oozie
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Servidor de Oozie |Nodos principales |11000 |HTTP |Dirección URL del servicio de Oozie |
| Servidor de Oozie |Nodos principales |11001 |HTTP |Puerto de administración de Oozie |

### <a name="ambari-metrics"></a>Métricas de Ambari
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Escala de tiempo (historial de aplicaciones) |Nodos principales |6188 |HTTP |La interfaz de usuario web del servicio de escala de tiempo |
| Escala de tiempo (historial de aplicaciones) |Nodos principales |30200 |RPC |La interfaz de usuario web del servicio de escala de tiempo |

### <a name="hbase-ports"></a>Puertos de HBase
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| HMaster |Nodos principales |16000 |&nbsp; |&nbsp; |
| Interfaz de usuario web de información de HMaster |Nodos principales |16010 |HTTP |El puerto de la interfaz de usuario web de HBase Master |
| Servidor de región |Todos los nodos de trabajo |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |El puerto que los clientes utilizan para conectarse a ZooKeeper |

### <a name="kafka-ports"></a>Puertos Kafka
| Servicio | Nodo(s) | Port | Protocol | Description |
| --- | --- | --- | --- | --- |
| Agente |Nodos de trabajo |9092 |[Protocolo de conexión de Kafka](http://kafka.apache.org/protocol.html) |Se utiliza para la comunicación del cliente |
| &nbsp; |Nodos Zookeeper |2181 |&nbsp; |El puerto que los clientes utilizan para conectarse a ZooKeeper |




<!--HONumber=Nov16_HO3-->


