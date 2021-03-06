---
title: "Cadena de conexión de MongoDB para la cuenta de DocumentDB | Microsoft Docs"
description: "Obtenga información sobre cómo conectar su aplicación de MongoDB a una cuenta de Azure DocumentDB utilizando una cadena de conexión de MongoDB."
keywords: "cadena de conexión de mongodb"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 0e947e0eaa6755f499860d5ce6d8bf354bc7eca4
ms.openlocfilehash: 35a281cf40cc5bb0a23c17990438ba696b22bc54


---

# <a name="connect-a-mongodb-app-to-an-documentdb-account-using-a-mongodb-connection-string"></a>Conexión de una aplicación de MongoDB a una cuenta de DocumentDB mediante una cadena de conexión de MongoDB
Obtenga información sobre cómo conectar su aplicación de MongoDB a una cuenta de Azure DocumentDB utilizando una cadena de conexión de MongoDB. Al conectar la aplicación de MongoDB a una base de datos de Azure DocumentDB, puede usar una base de datos de DocumentDB como almacén de datos de la aplicación de MongoDB. 

En este tutorial se proporcionan dos maneras de recuperar información de la cadena de conexión:

- [El método de inicio rápido](#QuickStartConnection), para su uso con controladores de .NET, Node.js, MongoDB Shell, Java y Python.
- [El método de la cadena de conexión personalizada](#GetCustomConnection), para su uso con otros controladores.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/). 
- Una cuenta de DocumentDB. Para obtener instrucciones, consulte [Creación de una cuenta de DocumentDB con soporte de protocolo para MongoDB mediante Azure Portal](documentdb-create-mongodb-account.md).

## <a name="a-idquickstartconnectionaget-the-mongodb-connection-string-using-the-quick-start"></a><a id="QuickStartConnection"></a>Obtención de la cadena de conexión de MongoDB utilizando el menú de inicio rápido
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **NoSQL (DocumentDB)**, seleccione la cuenta de DocumentDB con compatibilidad de protocolo para MongoDB. 
3. En la barra de **navegación izquierda** de la hoja de la cuenta, haga clic en **Inicio rápido**. 
4. Elija la plataforma (*controlador .NET*, *controlador Node.js*, *MongoDB Shell*, *controlador Java*, *controlador Python*). Si no ve el controlador o la herramienta en la lista, no se preocupe, documentamos constantemente más fragmentos de código de conexión. Comente a continuación lo que le gustaría ver y leer sobre [obtener información de la cadena de conexión de la cuenta](#GetCustomConnection) para aprender a crear su propia conexión.
5. Copie y pegue el fragmento de código en la aplicación de MongoDB y ya estará listo para empezar.

    ![Captura de pantalla de la hoja de inicio rápido](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a name="a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize"></a><a id="GetCustomConnection"></a> Obtención de la cadena de conexión de MongoDB para personalizar
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **NoSQL (DocumentDB)**, seleccione la cuenta de DocumentDB con compatibilidad de protocolo para MongoDB. 
3. En la barra de **navegación izquierda** de la hoja de la cuenta, haga clic en **Cadena de conexión**. 
4. Se abre la hoja **Información de cadena de conexión** con toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluida una cadena de conexión precreada.

    ![Captura de pantalla de la hoja Cadena de conexión](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de la cadena de conexión
> [!Important]
> DocumentDB tiene estrictos estándares y requisitos de seguridad. Las cuentas de DocumentDB requieren autenticación y comunicación segura a través de **SSL**.
>
>

Es importante tener en cuenta que DocumentDB admite el formato de identificador URI de cadena de conexión de MongoDB estándar, con un par de requisitos específicos: las cuentas de DocumentDB requieren la autenticación y la comunicación segura mediante SSL.  Por tanto, el formato de la cadena de conexión es:

    mongodb://username:password@host:port/[database]?ssl=true

Donde los valores de esta cadena están disponibles en la hoja Cadena de conexión mostrada antes.

* Username (obligatorio)
  * Nombre de la cuenta de DocumentDB
* Password (obligatorio)
  * Contraseña de la cuenta de DocumentDB
* Host (obligatorio)
  * FQDN de la cuenta de DocumentDB
* Port (obligatorio)
  * 10250
* Database (opcional)
  * La base de datos predeterminada usada por la conexión
* ssl=true (obligatorio)

Por ejemplo, considere la cuenta que aparece en la hoja Información de cadena de conexión mostrada antes.  Una cadena de conexión válida es:

    mongodb://contoso123:<password@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.
* Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md)de MongoDB.



<!--HONumber=Nov16_HO4-->


