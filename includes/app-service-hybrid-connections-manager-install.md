
1. En la hoja **Conexiones híbridas**, haga clic en la conexión híbrida que acaba de crear y, a continuación, en **Configuración del agente de escucha**.
   
    ![Click Listener Setup](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
2. Se abre la hoja **Propiedades de conexión híbrida** . En **Administrador de conexiones híbridas local**, seleccione **Descargar y configurar manualmente**, guarde el paquete HybridConnectionManager.msi descargado y copie la cadena de conexión de puerta de enlace.
   
    ![Haga clic aquí para instalar](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
3. Desde un símbolo del sistema de administrador, escriba el siguiente comando para iniciar el instalador:
   
        start HybridConnectionManager.msi
4. Después de ejecutarlo, haga clic en **Ahora no**; después, busque la carpeta %Archivos de programa%\Microsoft\HybridConnectionManager, ejecute HCMConfigWizard.exe y haga clic en **Sí** en el cuadro de diálogo **Control de cuentas de usuario**.
5. Pegue la cadena de conexión híbrida que ha copiado anteriormente y haga clic en **Aceptar**. 
   
    ![Instalación](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
6. Cuando finalice la instalación, haga clic en **Cerrar**.
   
    ![Click Close](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
   
    En la hoja **Conexiones híbridas**, la columna **Estado** ahora muestra **Conectado**. 
   
    ![Connected Status](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)



<!--HONumber=Nov16_HO3-->


