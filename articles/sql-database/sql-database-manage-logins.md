---
title: "Autorización y autenticación de SQL Database: concesión de acceso | Microsoft Docs"
description: "Obtenga información acerca de la administración de seguridad de la Base de datos SQL, específicamente el modo de administrar la seguridad del inicio de sesión y el acceso a la base de datos con la cuenta de entidad de seguridad a nivel de servidor."
keywords: "seguridad de la Base de datos SQL, administración de seguridad de la base de datos, seguridad de inicio de sesión, seguridad de la base de datos, acceso a la base de datos"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 0a65a93f-d5dc-424b-a774-7ed62d996f8c
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/14/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cec54dbc46194ff13fcafaa90f8a435edbd66598


---
# <a name="sql-database-authentication-and-authorization-granting-access"></a>Autorización y autenticación de Base de datos SQL: concesión de acceso
> [!div class="op_single_selector"]
> * [Tutorial introductorio](sql-database-get-started-security.md)
> * [Conceder acceso](sql-database-manage-logins.md)
> 
> 

Comience aquí si desea información general sobre los conceptos de acceso a Base de datos SQL para administradores, no administradores y roles.

## <a name="unrestricted-administrative-accounts"></a>Cuentas administrativas sin restricciones
Hay dos posibles cuentas administrativas con permisos sin restricciones para acceder a la base de datos maestra virtual y a todas las bases de datos de usuario. Estas cuentas se denominan cuentas de entidades de nivel de servidor.

### <a name="azure-sql-database-subscriber-account"></a>Cuenta de suscriptor de Base de datos SQL de Azure
Cuando se crea una instancia lógica de SQL, se crea también una cuenta de inicio de sesión único, que se denomina Cuenta de suscriptor de Base de datos SQL. Esta cuenta se conecta mediante la autenticación de SQL Server (nombre de usuario y contraseña). Esta cuenta es un administrador en la instancia del servidor lógico y en todas las bases de datos de usuario asociadas a dicha instancia. Los permisos de la cuenta del suscriptor no se pueden restringir. Solo puede existir una de estas cuentas.

### <a name="azure-active-directory-administrator"></a>Administrador de Azure Active Directory
Una cuenta de Azure Active Directory también se puede configurar como administrador. Esta cuenta puede ser un usuario individual de Azure AD, o bien puede ser un grupo de Azure AD con varios usuarios individuales de Azure AD. Opcionalmente, se puede configurar un administrador de Azure AD, pero es obligatorio configurarlo si desea utilizar la autenticación de Windows para que las cuentas de Azure AD se conecten a Base de datos SQL. Para más información sobre cómo configurar el acceso a Azure Active Directory, vea [Conexión a SQL Database o SQL Data Warehouse mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md) y [Compatibilidad de SSMS con Azure AD MFA con SQL Database y SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

### <a name="configuring-the-firewall"></a>Configuración del firewall
Cuando el firewall de nivel de servidor está configurado, tanto la cuenta de suscriptor de Azure SQL Database como la cuenta de Azure Active Directory pueden conectarse a la base de datos maestra y a todas las bases de datos de usuario. El firewall de nivel de servidor se puede configurar a través del portal. Una vez que se establece una conexión, también se pueden configurar reglas de firewall de nivel de servidor adicionales, para lo que se debe usar la instrucción [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) de Transact-SQL. Para más información acerca de cómo configurar el firewall, consulte [Configuración del firewall en la Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Ruta de acceso de administrador
Cuando el firewall de nivel de servidor está configurado correctamente, los administradores de la cuenta del suscriptor de Base de datos SQL y SQL Server de Azure Active Directory pueden conectarse mediante herramientas de cliente como SQL Server Management Studio o SQL Server Data Tools. Solo las herramientas más recientes proporcionan todas las características y funcionalidades. El diagrama siguiente muestra una configuración típica para las dos cuentas de administrador.
    ![Ruta de acceso de administrador](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Si se usa un puerto abierto en el firewall de nivel de servidor, los administradores pueden conectarse a cualquier Base de datos SQL.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Conexión a una base de datos mediante SQL Server Management Studio
Para obtener un tutorial de la conexión mediante SQL Server Management Studio, consulte [Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md).

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="additional-special-accounts"></a>Cuentas especiales adicionales
Base de datos SQL proporciona dos roles administrativos restringidas en la base de datos maestra virtual a la que se pueden agregar cuentas de usuario.

### <a name="database-creators"></a>Creadores de bases de datos
Las cuentas administrativas pueden crear nuevas bases de datos. Para crear una cuenta adicional que pueda crear bases de datos, es preciso crear un usuario en la base de datos maestra y agregar el usuario al rol de base de datos **dbmanager** especial. El usuario puede ser un usuario de una base de datos independiente o un usuario basado en un inicio de sesión de SQL Server en la base de datos maestra virtual.

1. Con una cuenta de administrador, conéctese a la base de datos maestra virtual.
2. Paso opcional: crear un inicio de sesión de autenticación de SQL Server mediante la instrucción [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) . Instrucción de ejemplo:
   
   ```
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Al crear un inicio de sesión o un usuario de una base de datos independiente, use una contraseña segura. Para obtener más información, consulte [Contraseñas seguras](https://msdn.microsoft.com/library/ms161962.aspx).
   > 
   > 
   
   Para mejorar el rendimiento, los inicios de sesión (entidades de nivel de servidor) se almacenan temporalmente en caché en el nivel de base de datos. Para actualizar la caché de autenticación, consulte [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).
3. En la base de datos maestra virtual, cree un usuario mediante la instrucción [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) . El usuario puede ser un usuario de base de datos independiente de Azure Active Directory (si ha configurado el entorno para la autenticación de Azure AD), o un usuario de base de datos independiente de autenticación de SQL Server o un usuario de autenticación de SQL Server basándose en un inicio de sesión de autenticación de SQL Server (creado en el paso anterior). Instrucciones de ejemplo:
   
   ```
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
   CREATE USER Tran WITH PASSWORD = '<strong_password>';
   CREATE USER Mary FROM LOGIN Mary; 
   ```
4. Agregue el nuevo usuario al rol de base de datos **dbmanager** mediante el uso de la instrucción [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) . Instrucciones de ejemplo:
   
   ```
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > dbmanager es un rol de base de datos de la base de datos maestra virtual, por lo que solo puede agregar un usuario al rol dbmanager. No puede agregar un inicio de sesión de nivel de servidor al rol de nivel de base de datos.
   > 
   > 
5. Si es necesario, configure el firewall de nivel de servidor para permitir que el nuevo usuario se conecte.

Ahora el usuario puede conectarse a la base de datos maestra virtual y puede crear nuevas bases de datos. La cuenta que crea la base de datos se convierte en la propietaria de la base de datos.

### <a name="login-managers"></a>Administradores de inicio de sesión
Si lo desea, puede realizar los mismos pasos (crear un inicio de sesión y un usuario, y agregar un usuario al rol **loginmanager** ) para que los usuarios puedan crear nuevos inicios de sesión en la base de datos maestra virtual. Normalmente no es necesario, ya que Microsoft recomienda utilizar los usuarios de la base de datos independiente que se autenticarán en el nivel de base de datos, en lugar de utilizar los usuarios basándose en los inicios de sesión. Para obtener más información, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Usuarios no administradores
Por lo general, las cuentas que no sean de administrador no necesitan acceso a la base de datos maestra virtual. Cree usuarios de una base de datos independiente en el nivel de base de datos con la instrucción [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) . El usuario puede ser un usuario de base de datos independiente de Azure Active Directory (si ha configurado el entorno para la autenticación de Azure AD), o un usuario de base de datos independiente de autenticación de SQL Server o un usuario de autenticación de SQL Server basándose en un inicio de sesión de autenticación de SQL Server (creado en el paso anterior). Para obtener más información, vea [Usuarios de base de datos independiente - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx). 

Para crear usuarios, conectarse a la base de datos y ejecutar instrucciones similares a los siguientes ejemplos:

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Inicialmente, solo pueden crear usuarios uno de los administradores o el propietario de la base de datos. Para autorizar que usuarios adicionales creen nuevos usuarios, conceda al usuario seleccionado el permiso `ALTER ANY USER` , para lo que debe emplear una instrucción como:

```
GRANT ALTER ANY USER TO Mary;
```

Para permitir un control total de la base de datos a usuarios adicionales, conviértalos en miembros del rol fijo de base de datos **db_owner** con la instrucción `ALTER ROLE`.

> [!NOTE]
> La razón principal para crear usuarios de base de datos basados en inicios de sesión es tener usuarios de autenticación de SQL Server que necesitan acceder a varias bases de datos. Los usuarios basados en inicios de sesión están vinculados al inicio de sesión y solo se mantiene una contraseña para dicho inicio de sesión. Los usuarios de base de datos independiente en bases de datos individuales son entidades individuales y cada uno mantiene su propia contraseña. Esto puede confundir a los usuarios de base de datos independiente si no mantienen sus contraseñas idénticas.
> 
> 

### <a name="configuring-the-database-level-firewall"></a>Configuración del firewall de nivel de base de datos
Como procedimiento recomendado, los usuarios no administradores solo deben tener acceso a través del firewall a las bases de datos que utilizan. En lugar de autorizar sus direcciones IP a través del firewall de nivel de servidor y permitir acceder a todas las bases de datos, use la instrucción [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) para configurar el firewall de nivel de base de datos. El firewall de nivel de base de datos no se puede configurar a través del portal.

### <a name="non-administrator-access-path"></a>Ruta de acceso de no administrador
Si el firewall de nivel de base de datos está configurado correctamente, los usuarios de la base de datos pueden conectarse mediante herramientas de cliente como SQL Server Management Studio o SQL Server Data Tools. Solo las herramientas más recientes proporcionan todas las características y funcionalidades. El siguiente diagrama muestra una ruta de acceso típica de no administrador.
![Ruta de acceso de no administrador](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupos y roles
La administración de acceso eficiente utiliza los permisos asignados a grupos y roles, en lugar de a usuarios individuales. Por ejemplo, si se utiliza la autenticación de Azure Active Directory:

* Coloque los usuarios de Azure Active Directory en un grupo de Azure Active Directory. Cree un usuario de base de datos independiente para el grupo. Coloque uno o varios usuarios de base de datos en un rol de base de datos. Y, a continuación, asigne permisos al rol de base de datos.

Si se utiliza la autenticación de SQL Server:

* Cree usuarios de bases de datos independientes en la base de datos. Coloque uno o varios usuarios de base de datos en un rol de base de datos. Y, a continuación, asigne permisos al rol de base de datos.

Los roles de base de datos pueden ser roles integrados como **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** y **db_denydatareader**. **db_owner** se usa habitualmente para conceder permiso completo solo a algunos usuarios. Los restantes roles fijos de base de datos son útiles para obtener rápidamente una base de datos simple en la fase de desarrollo, pero no se recomiendan para la mayoría de las bases de datos de producción. Por ejemplo, el rol fijo de base de datos **db_datareader** concede acceso de lectura a todas las tablas de la base de datos, algo que normalmente es más de lo estrictamente necesario. Es mucho mejor usar la instrucción [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) para crear sus propios roles de base de datos definidos por el usuario y otorgar cuidadosamente a cada rol los permisos mínimos necesarios para la necesidad empresarial. Cuando un usuario es miembro de varios roles, realiza la agregación de los permisos de todos ellos.

## <a name="permissions"></a>Permisos
En Base de datos SQL, hay más de 100 permisos que pueden conceder o denegar individualmente. Muchos de estos permisos están anidados. Por ejemplo, el permiso `UPDATE` de un esquema incluye el permiso `UPDATE` en cada tabla dentro de dicho esquema. Al igual que en la mayoría de los sistemas de permisos, la denegación de un permiso anula su concesión. Dada la naturaleza anidada y el número de permisos, es preciso realizar un estudio meticuloso para diseñar un sistema de permisos apropiado para proteger adecuadamente la base de datos. Empiece con la lista de permisos de [Permisos (motor de base de datos)](https://msdn.microsoft.com/library/ms191291.aspx) y revise el [gráfico a tamaño de póster](http://go.microsoft.com/fwlink/?LinkId=229142) de los permisos.

## <a name="next-steps"></a>Pasos siguientes
[Protección de bases de datos SQL](sql-database-security.md)

[Crear una tabla \(Tutorial\)](https://msdn.microsoft.com/library/ms365315.aspx)

[Insertar y actualizar datos en una tabla \(Tutorial\)](https://msdn.microsoft.com/library/ms365309.aspx)

[Leer datos de una tabla \(Tutorial\)](https://msdn.microsoft.com/library/ms365310.aspx)

[Crear vistas y procedimientos almacenados](https://msdn.microsoft.com/library/ms365311.aspx)

[Conceder acceso a un objeto de base de datos](https://msdn.microsoft.com/library/ms365327.aspx)

## <a name="additional-resources"></a>Recursos adicionales
[Protección de bases de datos SQL](sql-database-security.md)

[Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589.aspx) 




<!--HONumber=Nov16_HO3-->


