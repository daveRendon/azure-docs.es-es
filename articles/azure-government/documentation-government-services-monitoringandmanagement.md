---
title: "Administración y supervisión de Azure Government Azure Government | Microsoft Docs"
description: "Ofrece una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para Azure Government."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/31/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 722c53f819126791073575da04eded2ec5465764
ms.openlocfilehash: 9d4ba4eff1c9768a11c18cbf531b252d767bc9f2


---
# <a name="azure-government-monitoring-management"></a>Administración y supervisión de Azure Government
En este artículo se describen las variaciones y las consideraciones sobre los servicios de administración y supervisión en el entorno de Azure Government.

## <a name="automation"></a>Automatización
Automation está disponible normalmente en Azure Government.

### <a name="variations"></a>Variaciones
Las siguientes características de Automation no están actualmente disponibles en Azure Government.

* Creación de una credencial de entidad de servicio para la autenticación.

Para más información, consulte [Información general sobre Azure Automation](../automation/automation-intro.md).

## <a name="backup"></a>Copia de seguridad
El servicio Backup está disponible generalmente en Azure Government.

Para más información, consulte [Azure Government Backup](documentation-government-services-backup.md).

### <a name="variations"></a>Variaciones
Las siguientes características de Backup no están actualmente disponibles en Azure Government:

* Almacenes de Azure Resource Manager
* Administración mediante Azure Portal (se admite el Portal de Azure clásico)

## <a name="log-analytics"></a>Log Analytics
Log Analytics está disponible con carácter general en Azure Government.

### <a name="variations"></a>Variaciones
Las siguientes características y soluciones de Log Analytics no están disponibles actualmente en Azure Government.

* Soluciones que están en versión preliminar en Microsoft Azure, como por ejemplo:
  * Solución de supervisión de red
  * Solución Application Dependency Monitoring
  * Solución de Office 365
  * Solución Upgrade Analytics de Windows 10
  * Solución de Application Insights
  * Solución Azure Networking Analytics
  * Solución de Azure Automation Analytics
  * Solución de Key Vault Analytics
* Soluciones y características que requieren actualizaciones a software local, como por ejemplo:
  * Integración con System Center Operations Manager 2016 (se admiten versiones anteriores de Operations Manager)
  * Grupos de equipos de System Center Configuration Manager
  * Solución Surface Hub
* Características en versión preliminar de Azure público, como por ejemplo:
  * Exportación de datos a Power BI
* Métricas de Azure y diagnósticos de Azure
* Aplicaciones móviles de Operations Management Suite

Las direcciones URL para Log Analytics son diferentes en Azure Government:

| Azure Public | Azure Government | Notas |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portal de Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API de recopilador de datos](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Comunicación del agente: [configuración de los valores de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Comunicación del agente: [configuración de los valores de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Comunicación del agente: [configuración de los valores de firewall](../log-analytics/log-analytics-proxy-firewall.md) |

Las características siguientes de Log Analytics se comportan de otra manera en Azure Government:

* El agente de Windows se debe descargar del [portal de Log Analytics](https://oms.microsoft.us) para Azure Government.
* Para conectar el servidor de administración de System Center Operations Manager a Log Analytics, debe descargar e importar módulos de administración actualizados.
  1. Descargue y guarde los [módulos de administración actualizados](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Descomprima el archivo que descargó.
  3. Importe los módulos de administración en Operations Manager. Para más información sobre cómo importar un módulo de administración de un disco, consulte el tema [Importación de un módulo de administración de Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) en el sitio web de Microsoft TechNet.
  4. Para conectar Operations Manager a Log Analytics, siga los pasos descritos en [Conexión de Operations Manager con Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="frequently-asked-questions"></a>Preguntas más frecuentes
* ¿Se pueden migrar datos de Log Analytics en Microsoft Azure a Azure Government?
  * No. No se pueden migrar datos ni su área de trabajo de Microsoft Azure a Azure Government.
* ¿Se puede cambiar entre las áreas de trabajo de Microsoft Azure y Azure Government desde el portal de Log Analytics de Operations Management Suite?
  * No. Los portales de Microsoft Azure y Azure Government son independientes y no comparten información.

Para más información, consulte [Documentación pública de Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="site-recovery"></a>Site Recovery
Site Recovery está disponible en Azure Government con carácter general.

Para más información, consulte la [documentación pública de Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variaciones
Las siguientes características de Site Recovery no están actualmente disponibles en Azure Government:

* Almacenes de recuperación del sitio de Azure Resource Manager

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>



<!--HONumber=Nov16_HO3-->


