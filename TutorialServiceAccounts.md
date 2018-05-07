# ServiceAccounts
 Add accounts from the app Android
 
 # Este tutorial lo estoy realizando a los pocos iré añadiendo y modificando apartados conforme voy avanzando y encontrando errores.
 
 En este tutorial veremos como añadir nuestra app al apartado de cuentas del móvil para que nos registre con una cuenta que le añadamos
 y así se ejecute automáticamente sin necesidad de volver a introducir las credenciales, un método típico de las aplicaciones como Facebook,
 Whatsapp, etc.
 
 Aquí os dejo un tutorial que estoy siguiendo para realizar esto propio de Android:
 
 https://developer.android.com/reference/android/accounts/AbstractAccountAuthenticator
 
 /*----*/
 
 Para realizar esto hay que tener claro que es lo que vamos a realizar, podemos decirle a la app que para nuestro registro nos pida
 una serie de datos o bien que nos de también la opción de iniciar desde nuestra cuenta o cuentas añadidas en el móvil. En este caso yo le
 diré que me pida una serie de datos.
 
 En nuestro móvil Android tenemos un apartado en Ajustes-General llamado Cuentas, al abrirlo vemos una serie de apps añadidas que son las
 que el móvil tiene guardadas para que al abrirlas ya te inicien automáticamente, también podemos añadir nosotros más cuentas de las que 
 indica. 
 
 Como es lógico tenemos nuestra app creada e instalada en el móvil, pero, si le damos a añadir cuenta vemos que nuestra app no aparece esto
 se debe a que la aplicación le faltan una serie de clases, archivos xml y permisos para que ella pueda realizar esta función.
 
 # Las clases principales que serán 3:
 
 1- AccountAuthenticatorService.java (extends Service): Esta clase será la encargada de comunicarse con la cuenta.
 
 2- AccountAuthenticatorImpl.java (extends AbstractAccountAuthenticator): Esta clase realizará la función de crear, autorizar, etc nuestra cuenta.
 
 3- AccountManagerSimpleActivity.java (extends Activity): Esta clase realizará el inicio de sesión de usuario.
 
 # Los archivos xml son 2:
 
 1- account_preference.xml: Este xml nos devolverá la ventana de registro.
 
 2- authenticator.xml: Este xml se comunica con el account_preference.xml y añade una serie de datos.
 
 # Los permisos AndroidManifest.xml:
 
 <uses-permission android:name="android.permission.GET_ACCOUNTS" />
 <uses-permission android:name="android.permission.MANAGE_ACCOUNTS" />
 <uses-permission android:name="android.permission.AUTHENTICATE_ACCOUNTS" />
 <uses-permission android:name="android.permission.USE_CREDENTIALS" />
 
 *Más dentro del apartado <application
 
         <service
            android:name="AccountAuthenticatorService" //esta es la clase que crearemos
            android:exported="true"
            android:process=":auth">
            <intent-filter>
                <action android:name="android.accounts.AccountAuthenticator" />
            </intent-filter>
            <meta-data
                android:name="android.accounts.AccountAuthenticator"
                android:resource="@xml/authenticator" /> //el xml que también crearemos
        </service>
 
 
 
