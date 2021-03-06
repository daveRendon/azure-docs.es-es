---
title: "Conceptos clave de Administración de API"
description: "Obtenga información acerca de las API, los productos, los roles, los grupos y otros conceptos clave de Administración de API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: e71da405-835a-48f3-956f-45c1a85698d7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dcff834c9d4aff82e8319e6c23a74c2e49902a8c


---
# <a name="what-is-api-management"></a>¿Qué es Administración de API?
La Administración de API ayuda a las organizaciones a publicar API para desarrolladores externos, asociados e internos para liberar el potencial de sus datos y servicios. Todas y cada una de las empresas pretenden extender sus operaciones como una plataforma digital creando nuevos canales, buscando nuevos clientes y estrechando la relación con los existentes. Administración de API proporciona las capacidades esenciales para garantizar un programa API exitoso a través de compromisos con desarrolladores, conocimiento de los negocios, análisis, seguridad y protección.

Vea el siguiente vídeo para más información sobre Administración de API de Azure y aprender a usarla para agregar características a su API, incluido el control de acceso, limitación de tasa, supervisión, registro de eventos y almacenamiento en caché de respuestas, con el mínimo esfuerzo por su parte.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-API-Management-Overview/player]
> 
> 

Para usar Administración de API, los administradores crean API. Cada API consta de una o varias operaciones y se puede agregar a uno o varios productos. Para usar una API, los desarrolladores se suscriben a un producto que contiene esa API y después pueden llamar a la operación de la API cumpliendo cualquier directiva de uso que pueda estar en vigor.

En este tema se proporciona información general de los conceptos clave de Administración de API.

> [!NOTE]
> Para obtener más información, consulte las notas del producto en PDF [Administración de API basadas en la nube: aprovechamiento de la capacidad de las API](http://j.mp/ms-apim-whitepaper) . Estas notas del producto introductorias sobre la administración de API por CITO Research cubren: 
> 
> * Desafíos y requisitos comunes de API
> * Desacoplamiento de API y presentación de fachadas
> * Puesta en marcha de los desarrolladores rápidamente
> * Protección de acceso
> * Análisis y métricas
> * Obtención de control e información con una plataforma de administración de API
> * Uso de soluciones de nube frente a locales
> * Administración de API de Azure
> 
> 

## <a name="apis"> </a>API y operaciones
Las API son el fundamento de una instancia del servicio Administración de API. Cada API representa un conjunto de operaciones disponibles para los desarrolladores. Cada API contiene una referencia a un servicio back-end que implementa la API y sus operaciones se asignan a las operaciones implementadas por dicho servicio. Las operaciones de Administración de API son altamente configurables, con control sobre asignación de direcciones URL, parámetros de consulta y ruta de acceso, contenidos de solicitudes y respuestas y almacenamiento en caché de respuestas de operaciones. En la API o en el ámbito de operación individual, también se pueden implementar directivas de límite de tasa, cuotas y restricción de direcciones IP.

Para obtener más información, vea [Creación de API][Creación de API] e [Incorporación de operaciones a una API][Incorporación de operaciones a una API].

## <a name="products"> </a> Productos
Los productos son la forma de presentar las API a los desarrolladores. Los productos en Administración de API tienen una o varias API y se configuran con un título, una descripción y términos de uso. Los productos pueden ser de tipo **Abierto** o **Protegido**. Para poder usar los productos protegidos es necesario suscribirse antes a ellos, mientras que los productos abiertos pueden usarse sin suscripción. Cuando un producto está preparado para que lo usen los desarrolladores, se puede publicar. Una vez publicado, los desarrolladores pueden verlo (y, en el caso de los productos protegidos, suscribirse a él). La aprobación de la suscripción se configura en el ámbito de producto y puede requerir la aprobación del administrador o aprobarse automáticamente.

Los grupos se usan para administrar la visibilidad de productos a los desarrolladores. Los productos conceden visibilidad a los grupos y los desarrolladores pueden ver los productos visibles a los grupos a los que pertenecen y suscribirse a ellos. 

Para obtener más información, consulte [Creación y publicación de un producto][Creación y publicación de un producto] y el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="groups"> </a> Grupos
Los grupos se usan para administrar la visibilidad de productos a los desarrolladores. Administración de API tiene los siguientes grupos invariables del sistema.

* **Administradores** : los administradores de la suscripción de Azure son miembros de este grupo. Los administradores controlan las instancias del servicio Administración de API y crean las API, las operaciones y los productos que usan los desarrolladores.
* **Desarrolladores** : los usuarios del portal para desarrolladores autenticados se incluyen en este grupo. Los desarrolladores son los clientes que compilan aplicaciones con sus API. Los desarrolladores, después de que se les concede acceso al portal para desarrolladores, crean aplicaciones que llaman a las operaciones de una API.
* **Invitados** : a este grupo pertenecen los usuarios del portal para desarrolladores no autenticados como, por ejemplo, clientes potenciales que visitan el portal para desarrolladores de una instancia de Administración de API. Se les concede determinado acceso de solo lectura, como por ejemplo la posibilidad de ver API pero no llamarlas.

Además de estos grupos del sistema, los administradores pueden crear grupos personalizados o [aprovechar los grupos externos en inquilinos de Azure Active Directory asociados](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Los grupos personalizados y externos pueden usarse junto con grupos del sistema en la concesión a los desarrolladores de visibilidad y acceso a productos de la API. Por ejemplo, podría crear un grupo personalizado para los desarrolladores afiliados a una organización asociada específica y permitirles el acceso a las API a partir de un producto que contenga solo las API relevantes. Un usuario puede ser miembro de más de un grupo.

Para obtener más información, vea  [Creación y uso de grupos][Creación y uso de grupos].

## <a name="developers"> </a> Desarrolladores
Los desarrolladores representan las cuentas de usuario de una instancia del servicio Administración de API. Los desarrolladores pueden ser creados o invitados a unirse por administradores y también pueden suscribirse desde el [Portal para desarrolladores][Portal para desarrolladores]. Cada desarrollador es un miembro de uno o varios grupos y se puede suscribir a los productos que conceden visibilidad a dichos grupos.

Cuando los desarrolladores se suscriben a un producto, se les concede la clave principal y secundaria para dicho producto. Esta clave se usa cuando se realizan llamadas en las API del producto.

Para obtener más información, vea las secciones [Creación de un desarrollador o Invitación a un desarrollador][Creación de un desarrollador o Invitación a un desarrollador] y [Asociación de un grupo a un producto][Asociación de un grupo a un producto].

## <a name="policies"> </a> Directivas
Las directivas son una poderosa funcionalidad de Administración de API que permite al publicador cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Entre las declaraciones más usadas se encuentran la conversión de formato de XML a JSON y la limitación de tasa de llamadas para restringir la cantidad de llamadas entrantes de un desarrollador, pero también hay muchas otras directivas disponibles.

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) y [Establecer variable](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) se basan en expresiones de directiva. Para más información, consulte [Directivas avanzadas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [Expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx) y el vídeo siguiente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

Para obtener una lista completa de las directivas de API Management, consulte [Referencia de la directiva][Referencia de la directiva]. Para obtener más información acerca del uso y la configuración de directivas, consulte [Directivas de API Management][Directivas de API Management]. Para ver un tutorial sobre la creación de un producto con directivas de cuota y límite de tasas, consulte [Protección de su API con límites de frecuencia][Protección de su API con límites de frecuencia]. Para ver una demostración, vea el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="developer-portal"> </a> Portal para desarrolladores
El portal para desarrolladores es el lugar en el que los desarrolladores pueden aprender acerca de sus API, ver operaciones y llamarlas y suscribirse a productos. Los clientes potenciales pueden visitar el portal para desarrolladores, ver API y operaciones y suscribirse. La dirección URL del portal para desarrolladores se encuentra en el panel del Portal de Azure clásico para su instancia del servicio Administración de API.

Puede personalizar el aspecto y apariencia del portal para desarrolladores agregando contenido personalizado, personalizando estilos e incorporando su toque diferenciador.

## <a name="api-management-and-the-api-economy"></a>Administración de API y la economía de la API
Para más información acerca de Administración de API, vea la siguiente presentación de la conferencia Microsoft Ignite 2015.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3708/player]
> 
> 

[API y operaciones]: #apis
[Productos]: #products
[Grupos]: #groups
[Desarrolladores]: #developers
[Directivas]: #policies
[Portal para desarrolladores]: #developer-portal

[Creación de API]: api-management-howto-create-apis.md
[Incorporación de operaciones a una API]: api-management-howto-add-operations.md
[Creación y publicación de un producto]: api-management-howto-add-products.md
[Creación y uso de grupos]: api-management-howto-create-groups.md
[Asociación de grupos a desarrolladores]: api-management-howto-create-groups.md#associate-group-developer
[Creación y definición de configuraciones de productos avanzadas]: api-management-howto-product-with-rules.md
[Creación o invitación de desarrolladores]: api-management-howto-create-or-invite-developers.md
[Referencia de la directiva]: api-management-policy-reference.md
[Directivas de Administración de API]: api-management-howto-policies.md
[Creación de una instancia del servicio de API Management]: api-management-get-started.md#create-service-instance







<!--HONumber=Nov16_HO2-->


