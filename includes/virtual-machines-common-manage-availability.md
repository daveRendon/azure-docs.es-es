## <a name="understand-planned-vs-unplanned-maintenance"></a>Descripción del mantenimiento planeado frente al no planeado
Existen dos tipos de eventos de la plataforma Microsoft Azure que pueden afectar a la disponibilidad de sus máquinas virtuales: el mantenimiento planeado y el mantenimiento no planeado.

* **eventos de mantenimiento planeado** son actualizaciones periódicas realizadas por Microsoft en la plataforma Azure subyacente para mejorar en general la fiabilidad, el rendimiento y la seguridad de la infraestructura de la plataforma sobre las que se funcionan sus máquinas virtuales. La mayoría de estas actualizaciones se realizan sin las máquinas virtuales ni a los servicios en la nube resulten afectados. Sin embargo, en ocasiones estas actualizaciones requieren reiniciar la máquina virtual para aplicar las actualizaciones necesarias a la infraestructura de las plataformas.
* **eventos de mantenimiento no planeado** se producen cuando el hardware o la infraestructura física subyacente a su máquina virtual presentan algún tipo de error. Entre estos podemos encontrar errores de la red local, errores de los discos locales u otras errores a nivel de bastidor. Cuando se detecta un error de este tipo, la plataforma Azure automáticamente migrará su máquina virtual desde la máquina física averiada que aloja la máquina virtual hasta una máquina física sin problemas. Estos eventos no son habituales, pero pueden hacer que su máquina virtual se reinicie.

Para reducir el impacto del tiempo de parada debido a uno o más de estos eventos, recomendamos las siguientes mejores prácticas de alta disponibilidad para las máquinas virtuales:

* [Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia]
* [Configure cada nivel de aplicación en conjuntos separados de disponibilidad]
* [Combinación de un equilibrador de carga con conjuntos de disponibilidad]
* [Uso de varias cuentas de almacenamiento para cada conjunto de disponibilidad]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia
Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible y cumplirá el 99,95% de los niveles de servicio contratados de Azure. Para obtener más información, consulte [Acuerdo de Nivel de Servicio para máquinas virtuales](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite dejar una máquina virtual de instancia única sola en un conjunto de disponibilidad. Las máquinas virtuales en esta configuración no tienen derecho a la garantía de los contratos de nivel de servicio y sufrirán un tiempo de inactividad durante los eventos de Azure mantenimiento planeado.
> 
> 

La plataforma Azure subyacente asigna a cada máquina virtual del conjunto de disponibilidad un **dominio de actualización** y un **dominio de error**. Para un conjunto de disponibilidad dado, se asignan de forma predeterminada cinco dominios de actualización que el usuario no puede configurar (las implementaciones de Resource Manager pueden aumentarse para proporcionar un máximo de veinte dominios de actualización), con el fin de indicar grupos de máquinas virtuales y el hardware físico subyacente que se pueden reiniciar simultáneamente. Cuando se configuran más de cinco máquinas virtuales en un único conjunto de disponibilidad, la sexta máquina virtual se coloca en el mismo dominio de actualización que la primera, la séptima en el mismo que la segunda, y así sucesivamente. Es posible que el orden en que se reinician los dominios de actualización no siga una secuencia durante un mantenimiento planeado, pero se reinician de uno en uno.

Los dominios de error definen un grupo de máquinas virtuales que comparten un origen de alimentación y un interruptor de red comunes. De manera predeterminada, las máquinas virtuales configuradas en un conjunto de disponibilidad se separan en hasta 3 dominios de error en las implementaciones con Resource Manager (dos dominios de error en las implementaciones con el método clásico). Aunque colocar las máquinas virtuales en un conjunto de disponibilidad no protege su aplicación contra errores del sistema operativo ni específicos de aplicaciones, limita el impacto de posibles errores de hardware físico, interrupciones de red o cortes de alimentación.

<!--Image reference-->
   ![Dibujo conceptual de la configuración del dominio de actualización y de error](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configure cada nivel de aplicación en conjuntos separados de disponibilidad
Si las máquinas virtuales son casi idénticas y tienen la misma función en su aplicación, recomendamos que configure un conjunto de disponibilidad para cada nivel de la aplicación.  Si coloca dos niveles diferentes en el mismo conjunto de disponibilidad, todas las máquinas virtuales en un mismo nivel de aplicación se podrían reiniciar simultáneamente. Al configurar al menos 2 máquinas virtuales de un conjunto de disponibilidad, se garantiza que al menos esté disponible una en cada nivel.

Por ejemplo, podría poner todas las máquinas virtuales en el front-end de la aplicación que ejecuta IIS, Apache, Nginx, etc. en un solo conjunto de disponibilidad. Asegúrese de que solo las máquinas virtuales de front-end se colocan en el mismo conjunto de disponibilidad. De la misma manera, asegúrese de que solo las máquinas virtuales de niveles de datos se colocan en su propio conjunto de disponibilidad, por ejemplo, las máquinas virtuales replicadas de SQL Server o las de MySQL.

<!--Image reference-->
   ![Niveles de aplicación](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinación de un equilibrador de carga con conjuntos de disponibilidad
Combine [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) con un conjunto de disponibilidad para aprovechar al máximo la resistencia de la aplicación. El equilibrador de carga de Azure distribuye el tráfico entre varias máquinas virtuales. El equilibrador de carga de Azure está incluido en nuestras máquinas virtuales de niveles estándar. No todos los niveles de las máquinas virtuales incluyen Azure Load Balancer. Para obtener más información sobre el equilibrio de carga en máquinas virtuales, consulte [Equilibrio de carga de máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Si el equilibrador de carga no está configurado para equilibrar el tráfico entre varias máquinas virtuales, cualquier evento de mantenimiento planeado afectará a la única máquina virtual dedicada al tráfico, lo que provocará una interrupción en el nivel de la aplicación. Si se colocan varias máquinas virtuales del mismo nivel en el mismo equilibrador de carga y conjunto de disponibilidad, se permitirá tener un tráfico continuamente disponible asistido por, al menos, una instancia.

## <a name="use-multiple-storage-accounts-for-each-availability-set"></a>Uso de varias cuentas de almacenamiento para cada conjunto de disponibilidad
Hay procedimientos recomendados que deben seguirse con respecto a las cuentas de almacenamiento utilizadas por los discos duros virtuales en la máquina virtual. Cada disco (VHD) es un blob en páginas en una cuenta de Azure Storage. Es importante asegurarse de que hay aislamiento y redundancia en las cuentas de almacenamiento con el fin de proporcionar una alta disponibilidad para las máquinas virtuales en el conjunto de disponibilidad.

1. **Mantenga todos los discos (sistema operativo y datos) asociados a una máquina virtual en la misma cuenta de almacenamiento.**
2. **Los [límites](../articles/storage/storage-scalability-targets.md) de la cuenta de almacenamiento deben tenerse en cuenta** cuando se agregan más discos duros virtuales a una cuenta de almacenamiento.
3. **Utilice una cuenta de almacenamiento independiente para cada máquina virtual de un conjunto de disponibilidad.** Varias máquinas virtuales en el mismo conjunto de disponibilidad NO deben compartir la misma cuenta de almacenamiento. Las máquinas virtuales en distintos conjuntos de disponibilidad pueden compartir las cuentas de almacenamiento mientras se sigan los procedimientos recomendados anteriores.

<!-- Link references -->
[Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configure cada nivel de aplicación en conjuntos separados de disponibilidad]: #configure-each-application-tier-into-separate-availability-sets
[Combinación de un equilibrador de carga con conjuntos de disponibilidad]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Uso de varias cuentas de almacenamiento para cada conjunto de disponibilidad]: #use-multiple-storage-accounts-for-each-availability-set



<!--HONumber=Jan17_HO1-->


