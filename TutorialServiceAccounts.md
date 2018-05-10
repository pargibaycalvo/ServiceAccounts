# ServiceAccounts
 Add accounts from the app Android
 
 # Este tutorial lo estoy realizando a los pocos iré añadiendo y modificando apartados conforme voy avanzando y encontrando errores.
 
 En este tutorial veremos como añadir nuestra app al apartado de cuentas del móvil para que nos registre con una cuenta que le añadamos
 y así se ejecute automáticamente sin necesidad de volver a introducir las credenciales, un método típico de las aplicaciones como Facebook,
 Whatsapp, etc.
 
 Aquí os dejo los tutoriales que estoy siguiendo para realizar esto propio de Android:
 
 https://developer.android.com/reference/android/accounts/AbstractAccountAuthenticator
 
 https://github.com/jiahaoliuliu/AccountManagerSample
 
 https://github.com/gosyujin/MinimumAccountManagerSample
 
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
 
 # Archivos xml:
  
 1- authenticator.xml: Este xml se comunica con el account_preference.xml y añade una serie de datos.
 
 # Los permisos AndroidManifest.xml:
 
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.MANAGE_ACCOUNTS" />
    <uses-permission android:name="android.permission.AUTHENTICATE_ACCOUNTS" />
    <uses-permission android:name="android.permission.USE_CREDENTIALS" />
 
 *Más dentro del apartado <application
 
        <service android:name=".AccountAuthenticatorService" android:exported="true">
            <intent-filter>
                <action android:name="android.accounts.AccountAuthenticator" />
            </intent-filter>
            <meta-data
                android:name="android.accounts.AccountAuthenticator"
                android:resource="@xml/authenticator" />
        </service>
 
 
 # Empieza lo bueno
 
 Creamos la clase *AccountAuthenticatorService extends Service* nos dará un error, vamos a ese error y implementamos los métodos necesarios, dentro de la misma clase creamos la clase *AccountAuthenticatorImpl extends AbstractAccountAuthenticator* y realizamos lo mismo implementamos los métodos que nos pide. 
(Esto lo hago en la misma clase ya que son clases que se van a comunicar entre sí y es mñas fácil de ver el código, se puede hacer aparte las clases sin problemas).

Por último creamos la clase *AccountManagerSimpleActivity extends Activity*

**Estas clases las vamos a dejar vacías por el momento, así vemos que hace la app con nuestro móvil.

Dentro de la carpeta "res" de nuestro proyecto creamos el directorio "xml" y dentro del mismo creamos el archivo xml:

authenticator.xml:

    <?xml version="1.0" encoding="utf-8"?>
    <account-authenticator xmlns:android="http://schemas.android.com/apk/res/android"
    android:accountType="jp.example"
    android:icon="@drawable/logoapp"
    android:smallIcon="@drawable/logoapp"
    android:label="@string/app_name"
    />
    
**Aquí le añadiremos los iconos deseados el nombre de la app

Y por último añadimos los permisos más el service en el AndroidManifest.xml. Con esto todo reinstalamos la app y vamos a Cuentas de nuestro móvil, añadimos cuentas y podremos ver nuestra app que ya está en cuentas, pero por el momento no nos deja añadirla nos falta añadirle las funciones necesarias.

# Picar código en las clases:

**En la clase AccountAuthenticatorService, tenemos que llamar a la clase AccountAuthenticatorImpl ya que son 2 clases que una le dará servicio a la otra para que ejecute los métodos que queremos utilizar.

    public class AccountAuthenticatorService extends Service {

    private AccountAuthenticatorImpl mAuchenticator;

    @Override
    public void onCreate() {
        super.onCreate();
        mAuchenticator = new AccountAuthenticatorImpl(this);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mAuchenticator.getIBinder();
    }
    
La clase AccountAuthenticatorImpl se puede ver que nos implementó unos cuantos métodos, no hay que utilizarlos todos solo se utilizarán los métodos que queremos utilizar para nuestra app. Para saber que hacen todos estos métodos os dejo un video donde explica como y para que se usan https://www.youtube.com/watch?v=xS-ggopAD8w&t=154s.

En esta app utilizaremos 2 el **addAccount** que será el encargado de dejarnos añadir una cuenta y el **getAuthToken** para validar esas cuentas creadas, guardarlas y que tengan uso. 

Declaramos un Context con su constructor:

    private static class AccountAuthenticatorImpl extends AbstractAccountAuthenticator {
        private Context mContext;

        public AccountAuthenticatorImpl(Context context) {
            super(context);
            mContext = context;
        }

En el metodo **addAccount**, antes de nada crear una activityMain que tenga las credenciales que queráis para realizar el registro de la cuenta. Esta nos abre una activitymain para poder escribir nuestras credenciales. 

        @Override
        public Bundle addAccount(AccountAuthenticatorResponse response, String accountType, String authTokenType, String[] requiredFeatures, Bundle options) throws NetworkErrorException {
            Intent add = new Intent(mContext, AccountManagerSimpleActivity.class);
            add.putExtra(AccountManager.KEY_ACCOUNT_AUTHENTICATOR_RESPONSE, response);
            Bundle reply = new Bundle();
            reply.putParcelable(AccountManager.KEY_INTENT, add);
            return reply;
        }
        
Os dejo mi activityMain para que os hagáis una idea de como hacerlo simple:
        
    public class AccountManagerSimpleActivity extends Activity {

    private String username, password;
    private Button registration, cancel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_account_manager_simple);

        final EditText usernameEdit = (EditText) findViewById(R.id.editTextUser);
        final EditText passwordEdit = (EditText) findViewById(R.id.editTextUser);

        registration = (Button) findViewById(R.id.btnRegister);
        registration.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                username = usernameEdit.getText().toString();
                password = passwordEdit.getText().toString();
                attemptAuth(username, password);
            }

        });

        cancel = (Button) findViewById(R.id.btnCancel);
        cancel.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                finish();
            }
        });

    }

    private void attemptAuth(String username, String password) {
        Account account = new Account(username, "jp.example");
        AccountManager accountManager = AccountManager.get(this);
        accountManager.addAccountExplicitly(account, password, null);
        finish();
    }

 La función **attemptAuth** será la encargada de, los datos que introduzcamos validarlos y guardarlos a su vez una vez apruebe las credenciales finaliza el registro.
 
 Con esto tendremos una parte cubierta, si vamos a cuentas y queremos añadir nuestra app veremos como nos arranca la activityMain que creamos. Ahora le daremos uso al método **getAuthToken**:
 
         @Override
        public Bundle getAuthToken(AccountAuthenticatorResponse response, Account account, String authTokenType, Bundle options) throws NetworkErrorException {
            AccountManager am = AccountManager.get(mContext);
            String username = account.name;
            String password = am.getPassword(account);
            String token = "sample-no-token";

            Bundle result = new Bundle();
            result.putString(AccountManager.KEY_ACCOUNT_NAME, username);
            result.putString(AccountManager.KEY_ACCOUNT_TYPE, "jp.example");
            result.putString(AccountManager.KEY_AUTHTOKEN, token);
            return result;
        }
 
 Con este método le indicamos que nuestras credenciales tienen un usuario y contraseña, el token va a ser un mensaje que nos devolverá en nuestra mainActivity.
 
 Si ahora añadimos la cuenta, nos tendría que dejar pero esta cuenta a pesar de que está añadida y visiblemente parece que la va a usar cuando iniciemos la app no va a ser así, falta una función importante de sincronización el SyncAdapter.
 
 
 
 
