---
title: "Procedimientos recomendados de escalado automático en Azure Monitor | Microsoft Docs"
description: "Principios para usar eficazmente el escalado automático en Azure Monitor."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 9fa2b94b-dfa5-4106-96ff-74fd1fba4657
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f49d9121f34cc58d1486220a93bcb102f8eba90b


---
# <a name="best-practices-for-azure-monitor-autoscaling"></a>Procedimientos recomendados de escalado automático en Azure Monitor
Las secciones de este documento le sirven para conocer los procedimientos recomendados de escalado automático en Azure. Tras revisar esta información, podrá usar la opción de escalado automático de mejor forma en la infraestructura de Azure.

## <a name="autoscale-concepts"></a>Conceptos de escalado automático
* Un recurso solo puede tener *una* configuración de escalado automático.
* Una configuración de escalado automático puede tener uno o varios perfiles y cada perfil, a su vez, puede tener una o varias reglas de escalado automático.
* Una configuración de escalado automático escala instancias *horizontalmente* aumentando las instancias y las *reduce horizontalmente* disminuyendo el número de instancias.
  Una configuración de escalado automático presenta unos valores máximo, mínimo y predeterminado de instancias.
* Un trabajo de escalado automático siempre lee la métrica asociada por la que realizar la escala y comprueba si se rebasó el umbral establecido para el escalado horizontal o la reducción horizontal. En [Métricas comunes de escalado automático de Azure Monitor](insights-autoscale-common-metrics.md) encontrará una lista de métricas por las que el escalado automático puede escalar.
* Todos los umbrales se calculan en el nivel de instancia. Por ejemplo, "escalar horizontalmente por 1 instancia cuando el uso medio de CPU > 80 % cuando el número de instancias es 2" significa escalar horizontalmente cuando el uso medio de CPU en todas las instancias sea superior al 80 %.
* Las notificaciones de error siempre se reciben por correo electrónico. Las reciben, concretamente, el propietario, el colaborador y los lectores del recurso de destino. Además, siempre se recibe un correo electrónico de *recuperación* cuando el escalado automático se recupere de un error y comience a funcionar con normalidad.
* Puede optar por recibir una notificación de acción de escalado correcta por correo electrónico y por webhooks.

## <a name="autoscale-best-practices"></a>Procedimientos recomendados de escalado automático
Use los procedimientos recomendados al usar el escalado automático.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Asegúrese de que los valores máximo y mínimo son diferentes y de que hay margen suficiente entre ellos
Si tiene una configuración en la que el valor mínimo es 2, el valor máximo es 2 y el número de instancias es 2, no se puede ejecutar ninguna acción de escalado. Mantenga un margen suficiente entre los números de instancias máximo y mínimo, que son inclusivos. El escalado automático siempre escala entre estos límites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>El escalado manual se restablece al valor máximo y mínimo de escalado automático.
Si actualiza manualmente el recuento de instancias a un valor superior o inferior al máximo, el motor de escalado automático se ajusta automáticamente al valor mínimo (si está por debajo) o al máximo (si está por encima). Por ejemplo, establezca el intervalo entre 3 y 6. Si tiene una instancia en ejecución, el motor de escalado automático escala a 3 instancias cuando vuelva a ejecutarse. Del mismo modo, podría reducir horizontalmente de 8 instancias a 6 en su siguiente ejecución.  El escalado manual es muy temporal a menos que restablezca también las reglas de escalado automático.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Use siempre una combinación de reglas de escalado horizontal y reducción horizontal que realice un aumento y una disminución.
Si usa solo una parte de la combinación, el escalado automático escala o reduce horizontalmente (y a la inversa) hasta alcanzar el valor máximo o mínimo.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>No alterne entre el Portal de Azure y el Portal de Azure clásico al administrar el escalado automático
Para Cloud Services y App Services (Web Apps), use Azure Portal (portal.azure.com) para crear y administrar la configuración de escalado automático. Para los conjuntos de escala de máquinas virtuales, use PoSH, CLI o la API de REST para crear y administrar la configuración de escalado automático. No alterne entre el Portal de Azure clásico (manage.windowsazure.com) y el Portal de Azure (portal.azure.com) al administrar configuraciones de escalado automático. El Portal de Azure clásico y su back-end subyacente presentan una serie de limitaciones. Vaya al Portal de Azure para administrar el escalado automático mediante una interfaz gráfica de usuario. Las opciones son usar PowerShell de escalado automático, CLI o la API de REST (a través del Explorador de recursos de Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Elija la estadística adecuada para la métrica de diagnósticos
Para las métricas de diagnóstico, puede elegir entre *Promedio*, *Mínimo*, *Máximo* y *Total* como métrica a partir de la que escalar. La estadística más común es *Promedio*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Elija los umbrales cuidadosamente para todos los tipos de métrica
Es recomendable tener cuidado a la hora de elegir los diferentes umbrales de escalado y reducción horizontal en función de las situaciones prácticas.

*No se recomiendan* opciones de escalado automático como las de los siguientes ejemplos, con valores de umbral iguales o muy similares en condiciones de escalado o reducción horizontal:

* Aumentar las instancias en 1 cuando el número de subprocesos < = 600
* Disminuir las instancias en 1 cuando el número de subprocesos > = 600

Veamos un ejemplo de lo que puede llevar a producir un comportamiento confuso. Considere la siguiente secuencia.

1. Imaginemos que hay 2 instancias inicialmente y después aumenta el número promedio de subprocesos por instancia a 625.
2. El escalado automático escala horizontalmente agregando una tercera instancia.
3. Imaginemos ahora que el número promedio de subprocesos en la instancia se reduce a 575.
4. Antes de reducir verticalmente, el escalado automático intenta evaluar cuál será el estado final si reduce horizontalmente. Por ejemplo, 575 x 3 (número de instancias actual) = 1725/2 (número final de instancias al reducir verticalmente) = 862,5 subprocesos. Esto significa que el escalado automático tiene que volver a escalar horizontalmente de inmediato (incluso después de haber reducido horizontalmente) si el número promedio de subprocesos sigue siendo el mismo o incluso si se reduce solo una pequeña cantidad. Sin embargo, si se volviera a escalar verticalmente, todo el proceso se repetiría, dando lugar a un bucle infinito.
5. Para evitar esta situación (conocida como "inestable"), el escalado automático nunca reduce verticalmente. En su lugar, pasa esto por alto y vuelve a evaluar la situación la siguiente vez que el trabajo del servicio se ejecuta. Esto podría ser confuso para muchos usuarios, ya que puede dar la impresión de que el escalado automático no funciona cuando el número promedio de subprocesos es 575.

La estimación durante una reducción horizontal está pensada para evitar situaciones de inestabilidad. Conviene recordar este comportamiento cuando se elijan los mismos umbrales de escalado horizontal y reducción horizontal.

Nuestra recomendación es establecer un margen suficiente entre el escalado horizontal y en los umbrales. Por ejemplo, echemos un vistazo a esta siguiente combinación de reglas, que es mejor.

* Aumentar las instancias en 1 cuando el porcentaje de CPU > = 80
* Disminuir las instancias en 1 cuando el porcentaje de CPU > = 60

En este caso  

1. Imaginemos que hay 2 instancias para empezar.
2. Si el promedio de porcentaje de CPU entre instancias llega a 80, el escalado automático escala horizontalmente agregando una tercera instancia.
3. Imaginemos ahora que, con el tiempo, el porcentaje de CPU cae a 60.
4. La regla de reducción horizontal del escalado automático calcula el estado final como si fuese a reducirse horizontalmente. Por ejemplo, 60 x 3 (número de instancias actual) = 180/2 (número final de instancias al reducir verticalmente) = 90. Por tanto, el escalado automático no reduce horizontalmente porque tendría que volver a escalar horizontalmente de inmediato. En su lugar, omite la reducción vertical.
5. La próxima vez que comprueba el escalado automático, la CPU ha seguido cayendo a 50. Vuelve a calcular: 50 x 3 instancias = 150/2 instancias = 75, lo que está por debajo del umbral de escalado horizontal de 80. Por tanto, reduce horizontalmente a 2 instancias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Consideraciones para establecer valores de umbral de escalado en métricas especiales
 En el caso de las métricas especiales, como la métrica longitud de cola de bus de servicio o de almacenamiento, el umbral es el promedio de mensajes disponibles por número actual de instancias. Elija cuidadosamente el valor de umbral para esta métrica.

Veamos esto con un ejemplo para procurar que entienda el comportamiento de mejor forma.

* Aumentar las instancias en 1 cuando el número de mensajes de la cola de almacenamiento > = 50
* Disminuir las instancias en 1 cuando el número de mensajes de la cola de almacenamiento < = 10

Considere la siguiente secuencia:

1. Hay dos instancias de la cola de almacenamiento.
2. Siguen llegando mensajes y, al revisar la cola de almacenamiento, el recuento total es 50. Se supone que el escalado automático tendría que iniciar una acción de escalado horizontal. Sin embargo, vemos que sigue siendo de 50/2 = 25 mensajes por instancia. Por lo tanto, el escalado horizontal no se ha producido. Para que el primer escalado horizontal se produzca, el número total de mensajes en la cola de almacenamiento debe ser 100.
3. Tras esto, imaginemos que el número total de mensajes llega a 100.
4. Se agrega una tercera instancia de la cola de almacenamiento debido a una acción de escalado horizontal.  La siguiente acción de escalado horizontal no tendrá lugar hasta que el número total de mensajes en la cola llegue a 150 porque 150/3 = 50.
5. Ahora, disminuye el número de mensajes en la cola. Con tres instancias, la primera acción de reducción horizontal ocurre cuando el número total de mensajes en la cola llega a 30 porque 30/3 = 10 mensajes por instancia, que es el umbral de reducción horizontal.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Consideraciones de escalado cuando hay varios perfiles configurados en una configuración de escalado automático
En una configuración de escalado automático, puede elegir un perfil predeterminado, que se aplica siempre (independientemente de programaciones o períodos de tiempo), o bien optar por perfil periódico o por un perfil para un período fijo con una fecha y un intervalo de tiempo.

Cuando el servicio de escalado automático procesa estos perfiles, siempre los comprueba en el siguiente orden:

1. Perfil de fecha fija
2. Perfil periódico
3. Perfil predeterminado ("Siempre")

Si se cumple una condición de perfil, el escalado automático no comprobará la siguiente condición de perfil por debajo de este. El escalado automático solo procesa un perfil cada vez. Esto significa que, si queremos incluir una condición de procesamiento de un perfil de nivel inferior, tendremos que incluir también las reglas del perfil actual.

Analicemos esto con un ejemplo:

La siguiente imagen muestra una configuración de escalado automático con un perfil predeterminado con un número de instancias mínimo = 2 y un número de instancias máximo = 10. En este ejemplo, las reglas están configuradas para escalar horizontalmente cuando el número de mensajes en la cola sea mayor que 10 y, asimismo, reducir horizontalmente cuando el número de mensajes en la cola sea inferior a 3. Ahora el recurso puede escalar entre 2 y 10 instancias.

Además, hay un perfil periódico establecido para el lunes. Está configurado para un número de instancias mínimo = 2 y un número de instancias máximo = 12. Esto significa que, la primera vez que el escalado automático compruebe esta condición el lunes, si el recuento de instancias es 2, lo escala al nuevo mínimo de 3. Mientras el escalado automático siga encontrando una coincidencia con esta condición de perfil (lunes), solo procesa las reglas de escalado/reducción horizontal basadas en CPU configuradas en este perfil. En este momento, no comprueba la longitud de la cola. Sin embargo, si queremos que compruebe la condición de longitud de cola, tendremos que incluir esas reglas del perfil predeterminado también en el perfil del lunes.

De forma similar, cuando el escalado automático regresa al perfil predeterminado, primero comprueba si se cumplen las condiciones mínima y máxima. Si el número de instancias en ese momento es 12, reducirá horizontalmente a 10, que es el máximo permitido en el perfil predeterminado.

![configuración de escalado automático](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Consideraciones de escalado cuando hay varias reglas configuradas en un perfil
Hay casos en los que puede que sea necesario establecer varias reglas en un perfil. Cuando se establecen varias reglas, los servicios usan el siguiente conjunto de reglas de escalado automático.

Al *escalar horizontalmente*, el escalado automático se ejecuta si se cumple cualquier regla.
Al *reducir horizontalmente*, el escalado automático necesita que todas las reglas se cumplan.

Para ilustrar esto, imaginemos que tiene las siguientes 4 reglas de escalado automático:

* Con una CPU < 30 %, se reduce horizontalmente en 1
* Con una memoria < 50 %, se reduce horizontalmente en 1
* Con una CPU> 75 %, se escala horizontalmente en 1
* Con una memoria > 75 %, se escala horizontalmente en 1

Por tanto, sucederá lo siguiente:

* Con una CPU del 76 % y una memoria del 50 %, se escala horizontalmente.
* Con una CPU del 50 % y una memoria del 76 %, se escala horizontalmente.

Por otro lado, con una CPU del 25 % y una memoria del 51 %, el escalado automático **no** reduce horizontalmente. Para ello, la CPU debe ser 29 % y la memoria, 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Seleccione siempre un número predeterminado de instancias seguro
El número predeterminado de instancias es importante, porque el escalado automático escala el servicio a dicho número cuando no haya métricas disponibles. Por tanto, seleccione un número predeterminado de instancias que sea seguro para sus cargas de trabajo.

### <a name="configure-autoscale-notifications"></a>Configure notificaciones de escalado automático
El escalado automático notifica a los administradores y a los colaboradores del recurso por correo electrónico cuando se produce alguna de las siguientes condiciones:

* El servicio de escalado automático no puede realizar una acción.
* No hay métricas disponibles para que el servicio de escalado automático tome una decisión de escalado.
* Vuelve a haber métricas disponibles (recuperación) para poder tomar una decisión de escalado.
  Aparte de las condiciones anteriores, puede configurar notificaciones de correo electrónico o webhook para recibir una notificación cada vez que se lleve a cabo una acción de escalado correcta.




<!--HONumber=Nov16_HO3-->


