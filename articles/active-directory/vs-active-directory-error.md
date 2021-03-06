---
title: "Error durante la detección de autenticación"
description: "El asistente para conexión de Active Directory detectó un tipo de autenticación no compatible"
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7c031d6e04c26151b9e6e25fbca8acb6bbbdb12d


---
# <a name="error-during-authentication-detection"></a>Error durante la detección de autenticación
Al detectar el código de autenticación anterior, el asistente detectó un tipo de autenticación incompatible.   

## <a name="what-is-being-checked"></a>¿Qué se está comprobando?
**Nota:** para detectar correctamente el código de autenticación anterior de un proyecto, este debe estar creado.  Si se produjo este error y no tiene un código de autenticación anterior en el proyecto, vuelva a compilarlo e inténtelo de nuevo.

### <a name="project-types"></a>Tipos de proyecto
El asistente comprueba el tipo de proyecto que esté desarrollando, por lo que puede insertar la lógica de autenticación correcta en el proyecto.  Si no hay ningún controlador que derive de `ApiController` en el proyecto, el proyecto se considerará como un proyecto WebAPI.  Si solo hay controladores que derivan de `MVC.Controller` en el proyecto, el proyecto se considerará como proyecto MVC.  El asistente considera todo lo demás como no compatible.  Actualmente no son compatibles los proyectos WebForms.

### <a name="compatible-authentication-code"></a>Código de autenticación compatible
El asistente también comprueba la configuración de autenticación que se ha configurado previamente con el asistente o que es compatible con el asistente.  Si todos los valores de configuración están presentes, se considera un caso reentrante y el asistente abrirá y mostrará la configuración.  Si solo algunos valores de configuración están presentes, se considera un caso de error.

En un proyecto MVC, el asistente comprueba cualquiera de los siguientes valores de configuración, que se originan a partir de un uso anterior del asistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Además, el asistente comprueba los siguientes valores de configuración en el proyecto Web API, que se originan a partir del uso anterior del asistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Código de autenticación incompatible
Finalmente, el asistente trata de detectar versiones de código de autenticación que se hayan configurado con versiones anteriores de Visual Studio. Si recibió este error, significa el proyecto contiene un tipo de autenticación incompatible. El asistente detecta los siguientes tipos de autenticación de las versiones anteriores de Visual Studio:

* Autenticación de Windows 
* Cuentas de usuario individuales 
* Cuentas organizativas 

Para detectar la autenticación de Windows en un proyecto MVC, el asistente busca el elemento `authentication` en el archivo **web.config** .

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Para detectar la autenticación de Windows en un proyecto Web API, el asistente busca el elemento `IISExpressWindowsAuthentication` en el archivo **.csproj** del proyecto:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup> &lt;/Project&gt;
</pre>

Para detectar la autenticación de cuentas de usuario individuales, el asistente busca el elemento de paquete en el archivo **Packages.config** .

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Para detectar una forma anterior de autenticación con la cuenta de una organización, el asistente busca el siguiente elemento en **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para cambiar el tipo de autenticación, quite el tipo de autenticación incompatible y ejecute de nuevo el asistente.

Para obtener más información, consulte [Escenarios de autenticación en Azure AD](active-directory-authentication-scenarios.md).




<!--HONumber=Nov16_HO3-->


