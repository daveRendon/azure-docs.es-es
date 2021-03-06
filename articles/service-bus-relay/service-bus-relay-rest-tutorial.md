---
title: "Tutorial de REST de Service Bus con mensajería retransmitida | Microsoft Docs"
description: "Cree una sencilla aplicación host de retransmisión de Bus de servicio que expone una interfaz basada en REST."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7ba69a1a5f363fe5034e3fc7946b1584c9d77b50


---
# <a name="service-bus-wcf-relay-rest-tutorial"></a>Tutorial de REST de Service Bus WCF Relay
En este tutorial se describe cómo compilar una sencilla aplicación host de Bus de servicio que expone una interfaz basada en REST. REST permite a un cliente web, como un explorador web, tener acceso a la API de Bus de servicio a través de solicitudes HTTP.

En este tutorial se usa el modelo de programación REST de Windows Communication Foundation (WCF) para construir un servicio REST de Bus de servicio. Para obtener más información, vea [Modelo de programación REST de WCF](https://msdn.microsoft.com/library/bb412169.aspx) y [Diseño e implementación de servicios](https://msdn.microsoft.com/library/ms729746.aspx) en la documentación de WCF.

## <a name="step-1-create-a-service-namespace"></a>Paso 1: Creación de un espacio de nombres de servicio
El primer paso consiste en crear un espacio de nombres y obtener una clave de firma de acceso compartido (SAS). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través del Bus de servicio. El sistema genera una clave SAS automáticamente cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres de servicio y la clave SAS proporciona las credenciales de Bus de servicio para autenticar el acceso a una aplicación.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Paso 2: Definir un contrato de servicio de WCF basado en REST para utilizar con el Bus de servicio
Al igual que con otros servicios de Bus de servicio, cuando se crea un servicio de estilo REST, se debe definir el contrato. El contrato especifica qué operaciones admite el host. Una operación de servicio puede considerarse como un método de servicio web. Los contratos se crean mediante la definición de una interfaz de C++, C# o Visual Basic. Cada método de la interfaz corresponde a una operación de servicio específica. El atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) se debe aplicar a cada interfaz, y el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) se debe aplicar a cada operación. Si un método en una interfaz que tiene [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) no tiene [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), ese método no se expone. El código utilizado para estas tareas se muestra en el ejemplo que sigue al procedimiento.

La diferencia principal entre un contrato básico de Service Bus y un contrato de estilo REST es la adición de una propiedad a [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Esta propiedad permite asignar un método de la interfaz a un método en el otro lado de la interfaz. En este caso, usaremos [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) para vincular un método a HTTP GET. Esto permite al Bus de servicio recuperar e interpretar con precisión los comandos enviados a la interfaz.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para crear un contrato de Bus de servicio con una interfaz
1. Abra Visual Studio como administrador: haga clic con el botón derecho en el programa en el menú **Inicio** y después haga clic en **Ejecutar como administrador**.
2. Cree un nuevo proyecto de aplicación de consola. Haga clic en el menú **Archivo**, seleccione **Nuevo** y después **Proyecto**. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C#**, seleccione la plantilla **Aplicación de consola** y asígnele el nombre **ImageListener**. Use la **Ubicación** predeterminada. Haga clic en **Aceptar** para crear el proyecto.
3. Para un proyecto de C#, Visual Studio crea un archivo `Program.cs`. Esta clase contiene un método `Main()` vacío, necesario para que un proyecto de aplicación de consola se compile correctamente.
4. Agregue referencias a Service Bus y **System.ServiceModel.dll** al proyecto instalando el paquete NuGet de Service Bus. Este paquete agrega automáticamente referencias a las bibliotecas de Service Bus, así como a **System.ServiceModel** de WCF. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ImageListener** y haga clic en **Administrar paquetes NuGet**. Haga clic en la pestaña **Examinar** y luego busque `Microsoft Azure Service Bus`. Haga clic en **Instalar**y acepte las condiciones de uso.
5. Debe agregar explícitamente una referencia a **System.ServiceModel.Web.dll** en el proyecto:
   
    a. En el Explorador de soluciones, haga clic en la carpeta **Referencias** bajo la carpeta del proyecto y después haga clic en **Agregar referencia**.
   
    b. En el cuadro de diálogo **Agregar referencia**, haga clic en la pestaña **Marco** a la izquierda y, en el cuadro **Buscar**, escriba **System.ServiceModel.Web**. Active la casilla **System.ServiceModel.Web** y haga clic en **Aceptar**.
6. Agregue las siguientes instrucciones `using` al principio del archivo Program.cs.
   
    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) es el espacio de nombres que permite el acceso mediante programación a las características básicas de WCF. Bus de servicio utiliza muchos de los objetos y atributos de WCF para definir contratos de servicio. Usará este espacio de nombres en la mayoría de las aplicaciones de Retransmisión de bus de servicio. De forma similar, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) ayuda a definir el canal, que es el objeto a través del cual se comunica con Service Bus y el explorador web del cliente. Por último, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contiene los tipos que permiten crear aplicaciones basadas en web.
7. Cambie el nombre del espacio de nombres `ImageListener` a **Microsoft.ServiceBus.Samples**.
   
     ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Inmediatamente después de la llave de apertura de la declaración del espacio de nombres, defina una nueva interfaz denominada **IImageContract** y aplique el atributo **ServiceContractAttribute** a la interfaz con un valor de `http://samples.microsoft.com/ServiceModel/Relay/`. El valor del espacio de nombres difiere del espacio de nombres que utiliza en todo el ámbito de su código. El valor del espacio de nombres se utiliza como identificador único para este contrato, y debe tener información de la versión. Para obtener más información, vea [Control de versiones del servicio](http://go.microsoft.com/fwlink/?LinkID=180498). La especificación del espacio de nombres impide explícitamente que el valor del espacio de nombres predeterminado se agregue al nombre del contrato.
   
    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Dentro de la interfaz `IImageContract`, declare un método para la operación sencilla que el contrato `IImageContract` expone en la interfaz y aplique el atributo `OperationContractAttribute` al método que desea exponer como parte del contrato público del Bus de servicio.
   
    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. En el atributo **OperationContract**, agregue el valor **WebGet**.
    
    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Al hacerlo, permite que Service Bus enrute las solicitudes HTTP GET a `GetImage` y convierta los valores devueltos de `GetImage` en una respuesta HTTP GETRESPONSE. Más adelante en el tutorial, utilizará un explorador web para tener acceso a este método y mostrar la imagen en el explorador.
11. Inmediatamente después de la definición `IImageContract`, declare un canal que herede de las interfaces `IImageContract` y `IClientChannel`.
    
    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Un canal es el objeto de WCF a través del cual el servicio y el cliente se pasan información entre sí. Más adelante, creará el canal en su aplicación host. Service Bus usa después este canal para pasar las solicitudes HTTP GET del explorador a su implementación **GetImage**. Service Bus también usa el canal para tomar el valor devuelto de **GetImage** y convertirlo en HTTP GETRESPONSE para el explorador del cliente.
12. En el menú **Compilar**, haga clic en **Compilar solución** para confirmar la precisión del trabajo realizado hasta ahora.

### <a name="example"></a>Ejemplo
En el ejemplo de código siguiente se muestra una interfaz básica que define un contrato de Bus de servicio.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Paso 3: Implementar un contrato de servicio de WCF basado en REST para usar el Bus de servicio
La creación de un servicio de Bus de servicio de estilo REST requiere que se cree primero el contrato, que se define mediante una interfaz. El siguiente paso es implementar la interfaz. Esto implica la creación de una clase denominada **ImageService** que implementa la interfaz **IImageContract** definida por el usuario. Después de implementar el contrato, a continuación se configura la interfaz usando un archivo App.config. El archivo de configuración contiene la información necesaria para la aplicación, como el nombre del servicio, el nombre del contrato y el tipo de protocolo que se utiliza para comunicarse con Service Bus. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

Al igual que con los pasos anteriores, hay muy pocas diferencias entre implementar un contrato de estilo REST y un contrato básico de Bus de servicio.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Para implementar un contrato de Bus de servicio de estilo REST
1. Cree una nueva clase denominada **ImageService** directamente después de la definición de la interfaz **IImageContract**. La clase **ImageService** implementa la interfaz **IImageContract**.
   
    ```
    class ImageService : IImageContract
    {
    }
    ```
    Al igual que otras implementaciones de interfaz, puede implementar la definición en un archivo diferente. Sin embargo, para este tutorial, la implementación aparece en el mismo archivo que la definición de interfaz y el método `Main()`.
2. Aplique el atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) a la clase **IImageService** para indicar que la clase es una implementación de un contrato de WCF.
   
    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Como se mencionó anteriormente, este espacio de nombres no es un espacio de nombres tradicional. Más bien, forma parte de la arquitectura de WCF que identifica el contrato. Para más información, vea el tema [Nombres de contratos de datos](https://msdn.microsoft.com/library/ms731045.aspx) en la documentación de WCF.
3. Agregue una imagen .jpg al proyecto.  
   
    Se trata de una imagen que el servicio muestra en el explorador de recepción. Haga clic con el botón derecho en el proyecto y, después, haga clic en **Agregar**. A continuación, haga clic en **Elemento existente**. Utilice el cuadro de diálogo **Agregar elemento existente** para buscar un .jpg adecuado y después haga clic en **Agregar**.
   
    Al agregar el archivo, asegúrese de que está seleccionada la opción **Todos los archivos** en la lista desplegable situada junto al campo **Nombre de archivo:**. En el resto de este tutorial se supone que el nombre de la imagen es "image.jpg". Si tiene un archivo diferente, debe cambiar el nombre de la imagen o cambiar su código para compensar.
4. Para asegurarse de que el servicio en ejecución pueda encontrar el archivo de imagen, en el **Explorador de soluciones**, haga clic con el botón derecho en dicho archivo y luego en **Propiedades**. En el panel **Propiedades**, establezca **Copiar en el directorio de salida** en **Copiar si es posterior**.
5. Agregue una referencia al ensamblado **System.Drawing.dll** al proyecto, además de las siguientes instrucciones `using` asociadas.  
   
    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. En la clase **ImageService**, agregue el siguiente constructor que carga el mapa de bits y se prepara para enviarlo al explorador del cliente.
   
    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Directamente después del código anterior, agregue el siguiente método **GetImage** en la clase **ImageService** para devolver un mensaje HTTP que contiene la imagen.
   
    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Esta implementación usa **MemoryStream** para recuperar la imagen y prepararla para el streaming al explorador. Este inicia la posición de la secuencia en cero, declara el contenido de la secuencia como jpeg y transmite la información.
8. En el menú **Compilar**, haga clic en **Compilar solución**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir la configuración para ejecutar el servicio web en el Bus de servicio
1. En el **Explorador de soluciones**, haga doble clic en **App.config** para abrirlo en el editor de Visual Studio.
   
    El archivo **App.config** se parece a un archivo de configuración de WCF e incluye el nombre del servicio, el punto de conexión (es decir, la ubicación que Service Bus expone para que clientes y hosts se comuniquen entre sí) y el enlace (el tipo de protocolo que se usa para la comunicación). La principal diferencia aquí es que el punto de conexión de servicio configurado hace referencia a un enlace [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx), que no forma parte de .NET Framework.
2. El elemento XML `<system.serviceModel>` es un elemento de WCF que define uno o varios servicios. En este caso, se utiliza para definir el nombre del servicio y el extremo. En la parte inferior del elemento `<system.serviceModel>` (pero aún dentro de `<system.serviceModel>`), agregue un elemento `<bindings>` con el siguiente contenido. Esto define los enlaces utilizados en la aplicación. Puede definir varios enlaces, pero para este tutorial está definiendo solo uno.
   
    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    Este paso define un enlace [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) de Service Bus con **relayClientAuthenticationType** establecido en **Ninguno**. Este valor indica que un extremo que usa este enlace no requiere una credencial de cliente.
3. Después del elemento `<bindings>`, agregue un elemento `<services>`. Al igual que los enlaces, puede definir varios servicios en un solo archivo de configuración. Sin embargo, para este tutorial, solo se define uno.
   
    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    Este paso configura un servicio que utiliza el valor predeterminado definido anteriormente **webHttpRelayBinding**. También usa el valor predeterminado **sbTokenProvider**, que se define en el paso siguiente.
4. Después del elemento `<services>`, cree un elemento `<behaviors>` con el contenido siguiente, pero reemplace "SAS_KEY" por la clave de *firma de acceso compartido* (SAS) que obtuvo antes en [Azure Portal][Portal de Azure].
   
    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. Aún en App.config, en el elemento `<appSettings>`, reemplace todo el valor de la cadena de conexión por la cadena de conexión que obtuvo antes en el portal. 
   
    ```
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
6. Desde el menú **Compilar**, haga clic en **Compilar solución** para compilar la solución completa.

### <a name="example"></a>Ejemplo
El código siguiente muestra la implementación de contrato y servicio para un servicio basado en REST que se ejecuta en Service Bus con el enlace **WebHttpRelayBinding**.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

En el ejemplo siguiente se muestra el archivo App.config asociado con el servicio.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Paso 4: Hospedar el servicio de WCF basado en REST para usar el Bus de servicio
Este paso describe cómo ejecutar un servicio web mediante una aplicación de consola en el Bus de servicio. En el ejemplo que sigue al procedimiento se proporciona una lista completa del código escrito en este paso.

### <a name="to-create-a-base-address-for-the-service"></a>Para crear una dirección base para el servicio
1. En la declaración de la función `Main()`, cree una variable para almacenar el espacio de nombres del proyecto del Bus de servicio. Asegúrese de reemplazar `yourNamespace` por el nombre del espacio de nombres del servicio que creó antes.
   
    ```
    string serviceNamespace = "yourNamespace";
    ```
    El Bus de servicio utiliza el nombre del espacio de nombres para crear un URI único.
2. Cree una instancia `Uri` para la dirección base del servicio que se basa en el espacio de nombres.
   
    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Para crear y configurar el host del servicio web
* Cree el host del servicio web, utilizando la dirección URI creada anteriormente en esta sección.
  
    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    El host de servicio es el objeto de WCF que crea una instancia de la aplicación host. En este ejemplo se pasa el tipo de host que desea crear (un **ImageService**) y también la dirección en la que desea exponer la aplicación host.

### <a name="to-run-the-web-service-host"></a>Para ejecutar el host del servicio web
1. Abra el servicio.
   
    ```
    host.Open();
    ```
    El servicio está ahora en ejecución.
2. Muestre un mensaje que indica que el servicio se está ejecutando y cómo detenerlo.
   
    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Cuando termine, cierre el host del servicio.
   
    ```
    host.Close();
    ```

## <a name="example"></a>Ejemplo
En el ejemplo siguiente se incluye el contrato de servicio y la implementación de los pasos anteriores en el tutorial y se hospeda el servicio en una aplicación de consola. Compile el código siguiente en un archivo ejecutable denominado ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilación del código
Después de compilar la solución, haga lo siguiente para ejecutar la aplicación:

1. Presione **F5** o busque la ubicación del archivo ejecutable (ImageListener\bin\Debug\ImageListener.exe) para ejecutar el servicio. Mantenga la aplicación en ejecución, ya que se necesita para realizar el paso siguiente.
2. Copie y pegue la dirección desde el símbolo del sistema en un explorador para ver la imagen.
3. Cuando haya terminado, presione **ENTRAR** en la ventana del símbolo del sistema para cerrar la aplicación.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado una aplicación que utiliza el servicio de Retransmisión de bus de servicio, vea los artículos siguientes para obtener más información acerca de la mensajería retransmitida:

* [Información general sobre la arquitectura de Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Uso del servicio Service Bus WCF Relay](service-bus-dotnet-how-to-use-relay.md)

[Portal de Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


