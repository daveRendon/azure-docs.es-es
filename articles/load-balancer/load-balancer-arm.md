---
title: 'Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga: vista previa | Microsoft Docs'
description: Uso de PowerShell para el Equilibrador de carga con la versión preliminar de Azure Resource Manager (ARM). Uso de plantillas para el equilibrador de carga
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2016
ms.author: sewhee

---
# Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga
Azure Resource Manager es el marco de administración de servicios preferido en Azure. Azure Load Balancer puede administrarse ahora mediante las herramientas y las API basadas en Azure Resource Manager.

## Conceptos
Con Resource Manager, Azure Load Balancer contiene los siguientes recursos secundarios:

* Configuración de dirección IP front-end: un equilibrador de carga puede incluir una o varias direcciones IP front-end, conocidas también como IP virtuales (VIP). Estas direcciones IP sirven como entrada para el tráfico.
* Grupo de direcciones back-end: se trata de direcciones IP asociadas a las tarjetas de interfaz de red (NIC) de máquina virtual a las que se distribuirá la carga.
* Reglas de equilibrio de carga: una propiedad de regla asigna una combinación dada de dirección IP front-end y puerto a un conjunto de combinaciones de direcciones IP back-end y puerto. Un solo equilibrador de carga puede tener varias reglas de equilibrio de carga. Cada regla tiene una combinación de una IP de front-end y un puerto y una IP de back-end asociados a las máquinas virtuales.
* Sondeos: los sondeos permiten realizar un seguimiento del estado de las instancias de máquina virtual. Si se produce un error en un sondeo de estado, la instancia de VM se sacará automáticamente de la rotación.
* Reglas NAT entrantes: las reglas NAT que definen el tráfico entrante que fluye a través de la dirección IP front-end y se distribuye a la dirección IP back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## Plantillas de inicio rápido
El Administrador de recursos de Azure le permite aprovisionar sus aplicaciones mediante una plantilla declarativa. En una plantilla, puede implementar varios servicios junto con sus dependencias. Use la misma plantilla para implementar su aplicación de forma repetida durante cada fase de su ciclo de vida.

Las plantillas pueden incluir definiciones de máquinas virtuales, redes virtuales, conjuntos de disponibilidad, interfaces de red (NIC), cuentas de almacenamiento, equilibradores de carga, grupos de seguridad de red y direcciones IP públicas. Con las plantillas puede crear todo lo que necesita para una aplicación compleja. El archivo de plantilla se puede proteger en el sistema de administración de contenidos para controlar versiones y colaborar.

[Más información sobre las plantillas](http://go.microsoft.com/fwlink/?LinkId=544798)

[Más información sobre los recursos de red](../virtual-network/resource-groups-networking.md)

Las plantillas de inicio rápido que usan Azure Load Balancer se pueden encontrar en un [repositorio de GitHub](https://github.com/Azure/azure-quickstart-templates) que hospeda un conjunto de plantillas generadas por la comunidad.

Ejemplos de plantillas:

* [Dos máquinas virtuales en un Equilibrador de carga y reglas de equilibrio de carga](http://go.microsoft.com/fwlink/?LinkId=544799)
* [Dos máquinas virtuales en una red virtual con un Equilibrador de carga interno y reglas del Equilibrador de carga](http://go.microsoft.com/fwlink/?LinkId=544800)
* [Dos máquinas virtuales en un Equilibrador de carga y configuración de reglas NAT en el Equilibrador de carga](http://go.microsoft.com/fwlink/?LinkId=544801)

## Configuración del Equilibrador de carga de Azure con PowerShell o CLI
Introducción a los cmdlets de Azure Resource Manager, las herramientas de línea de comandos y las API de REST

* Los [cmdlets de Redes de Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) pueden usarse para crear un equilibrador de carga.
* [Cómo crear un Equilibrador de carga mediante el Administrador de recursos de Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Uso de CLI de Azure con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md)
* [API de REST del Equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## Pasos siguientes
También puede [empezar a crear un equilibrador de carga orientado a Internet](load-balancer-get-started-internet-arm-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento especifico del tráfico de red del equilibrador de carga.

Aprenda a administrar la [configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Esto es importante cuando la aplicación necesita mantener las conexiones activas para servidores detrás de un equilibrador de carga.

<!---HONumber=AcomDC_0921_2016-->