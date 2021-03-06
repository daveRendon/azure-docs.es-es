1. En su proyecto de **aplicación**, abra el archivo `AndroidManifest.xml`. En el código que aparece en los próximos dos pasos, reemplace *`**my_app_package**`* por el nombre del paquete de la aplicación del proyecto, que es el valor del atributo `package` de la etiqueta `manifest`.
2. Agregue los siguientes permisos nuevos después del elemento `uses-permission` existente:
   
        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Agregue el siguiente código después de la etiqueta de apertura `application` :
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Abra el archivo *ToDoActivity.java*y agregue la siguiente instrucción de importación:
   
        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. Agregue la siguiente variable privada a la clase: reemplace *`<PROJECT_NUMBER>`* por el número de proyecto que Google ha asignado a la aplicación en el procedimiento anterior:
   
        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. Cambie la definición de la *MobileServiceClient* de **privado** a **público estático**; ahora tendrá el siguiente aspecto:
   
        public static MobileServiceClient mClient;
7. A continuación, necesitamos agregar una nueva clase para controlar las notificaciones. En el Explorador de proyectos, abra los nodos **src** => **main** => **java** y haga clic con el botón derecho en el nodo del nombre del paquete: haga clic en **Nuevo** y en **Clase Java**.
8. En **Nombre** escriba `MyHandler` y, a continuación, haga clic en **Aceptar**.

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. En el archivo MyHandler, reemplace la declaración de clase con
   
        public class MyHandler extends NotificationsHandler {
10. Agregue las siguientes instrucciones para la clase `MyHandler` :
   
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. A continuación, agregue este miembro a la clase `MyHandler` :
   
        public static final int NOTIFICATION_ID = 1;
12. En la clase `MyHandler` , agregue el siguiente código para invalidar el método **onRegistered** , que registra el dispositivo con el centro de notificaciones del servicio móvil.
   
        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);
   
           new AsyncTask<Void, Void, Void>() {
   
               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
13. En la clase `MyHandler` , agregue el siguiente código para invalidar el método **onReceive** , que ocasiona que se visualice la notificación al recibirla.
   
        @Override
        public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");
   
               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags
   
               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();
   
               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
14. En el archivo TodoActivity.java, actualice el método **onCreate** de la clase *ToDoActivity* para registrar la clase de controlador de notificación. Asegúrese de agregar este código después de crear una instancia de *MobileServiceClient* .

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.


<!--HONumber=Dec16_HO2-->


