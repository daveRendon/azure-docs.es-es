---
title: "Tabla de límites de análisis de transmisión"
description: "Describe los límites del sistema y los tamaños recomendados para componentes y conexiones de Análisis de transmisiones."
services: stream-analytics
documentationcenter: NA
author: jeffstokes72
manager: paulettm
editor: cgronlun
ms.service: stream-analytics
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: e664ce9426a2852a35dfdade5d41a9ce8b37a3b7
ms.openlocfilehash: b4cfc4663dbef820c866846d65b9fa8512c7dcff


---
| Identificador de límites | Límite | Comentarios |
| --- | --- | --- |
| Número máximo de unidades de streaming por suscripción por región |50 |Para realizar una solicitud para aumentar las unidades de streaming de su suscripción hasta más de 50, póngase en contacto con el [Soporte técnico de Microsoft](https://support.microsoft.com/en-us). |
| Rendimiento máximo de una unidad de streaming |1MB/s* |El rendimiento máximo por unidad de streaming depende del escenario. El rendimiento real puede ser inferior y depende de la complejidad y partición de la consulta. Encontrará más detalles en el artículo [Escalación de trabajos de Azure Stream Analytics](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Número máximo de entradas por trabajo |60 |Hay un límite máximo de 60 entradas por trabajo de Análisis de transmisiones. |
| Número máximo de salidas por trabajo |60 |Hay un límite máximo de 60 salidas por trabajo de Análisis de transmisiones. |
| Número máximo de funciones por trabajo |60 |Hay un límite máximo de 60 funciones por trabajo de Análisis de transmisiones. |
| Número máximo de trabajos por región |1.500 |Cada suscripción puede tener hasta 1500 trabajos por región geográfica. |




<!--HONumber=Nov16_HO3-->


