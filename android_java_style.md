#Android Uygulama Geliştirme Prensipleri	
---------------------------

Bu uygulamanın amacı Android uygulama geliştirme prensiplerini belirlemektir. Uygulama geliştirirken bu dokümana göre hareket etmek temiz ve istikrarlı kod yazımında bize yardımcı olacaktır.

Not: Bazı yerlerde İngilizce-Türkçe kullanımında karışıklık olmaktadır. Bunlar kullanılan kelimelerin tam karşılığının olmamasından kaynaklanmaktadır. Örneğin; handle etmek , class dosyaları..

##1. Uygulama Prensipleri

###1.1 Proje Yapısı

Uygulama geliştirirken, proje yapısı ağağıdaki gibi olmadır. :

	src/AndroidTest
	src/Test
	src/CommonTest
	src/main
	

**AndroidTest** - Fonksiyonel testleri içeren klasör.    
**Test** - Unit testleri içeren klasör.  
**CommonTest** - Paylaşılan AndroidTest & Test kodlarının bulunduğu klasör. 
**main** - Uygulamanın kodlarını içeren klasör. 

Uygulamanın genel yapısı herhangi bir değişiklikte ya da yeni bir özellik eklendiğinde yukarıda belirlenmiş şekilde kalmalıdır. Bu yapıyı kullanmanın avantajı test ile ilişkili olan kodların ayrı olmasıdır. 

###1.2 Dosya İsimlendirme

####1.2.1 Class dosyaları

Her class dosyası BüyükKüçük şeklinde tanımlanmaldır. Örnek olarak;

	AndroidActivity, NetworkHelper, UserFragment, PerActivity

Android kütüphanesinden kalıtılmış bileşen **her zaman** hangi sınıftan kalıtılıyorsa o bileşenin ismi ile bitmelidir. Örnek olarak;

	UserFragment, SignUpActivity, RateAppDialog, PushNotificationServer, NumberView

BüyükKüçük harf şeklinde kullanılan class isimleri okunabilirlik açısından kolaylık sağlamaktadır. Ayrıca class'ları bileşenlerin isimlerine göre isimlendirme de hangi class'ın ne için kullanıldığı hakkında bize bilgi vermektedir. Örnek olarak RegistrationDialog bize kayıt ile ilgili işlemin bu dialog'da yapıldığını göstermektedir. 
	
####1.2.1 Resource Dosyaları

Resource dosyalarını isimlendirirken küçük-harf kullanmalıyız ve boşluk yerine alt çizgi (_) kullanmalıyız. Örnek olarak;

	activity_main, fragment_user, item_post

Bu şekilde kullanım, herhangi bir amaca yönelik oluşturulmuş layout dosyasını bulmamızda bize kolaylık sağlar. Android Studio'da layout kısmı alfabetik olarak sıralanmış durumdadır. Böylece activity_main şeklinde isim verildiğinde tüm aynı amaca hizmet eden layout'lar gruplanmış olacaktır.  

####1.2.2.1 Drawable Dosyaları

Drawable resource dosyaları **ic_name_00dp** şeklinde adlandırılmalıdır.. 

	ic_entrance_24dp , ic_accept_32dp

Bu şekilde bir isimlendirme, drawable klasörlerinde bulunan ikonların boyutlarının açılmadan öğrenilmesine yardımcı olacaktır. 

Diğer drawable bileşenlerinin kullanımı aşağıdaki şekilde olmalıdır. 

| Tipi       | Ön Eki    | Örnek                |
|------------|-----------|------------------------|
| Selector   | selector_ | selector_button_cancel |
| Background | bg_       | bg_rounded_button      |
| Circle     | circle_   | circle_white           |
| Progress   | progress_ | progress_circle_purple |
| Divider    | divider_  | divider_grey           |

Bu şekilde isimlendirme benzer bileşenlerin Android Studio ile gruplandırılmasına yardımcı olmaktadır. Aynı zamanda hangi bileşenin nerede kullanıldığına dair bilgi vermektedir. Doğru isimlendirme uygulama geliştirirken meydana gelecek ikilikleri engellemektedir. 

Selector state resource dosyası oluşurken de duruma göre son ek vermemiz gerekmektedir. 

| Durum    | Son Ek    | Örnek             |
|----------|-----------|---------------------|
| Normal   | _normal   | btn_accept_normal   |
| Pressed  | _pressed  | btn_accept_pressed  |
| Focused  | _focused  | btn_accept_focused  |
| Disabled | _disabled | btn_accept_disabled |
| Selected | _selected | btn_accept_selected |


####1.2.2.2 Layout Dosyaları

Layout dosyaları aşağıdaki şekilde Java class'ına göre oluşturulmalıdır. 
**class_java_name**

| Bileşen          | Class İsmi      | Layout İsmi       |
|------------------|-----------------|-------------------|
| Activity         | MainActivity    | activity_main     |
| Fragment         | MainFragment    | fragment_main     |
| Dialog           | RateDialog      | dialog_rate       |
| Widget           | UserProfileView | view_user_profile |
| AdapterView Item | N/A             | item_follower     |


####1.2.2.3 Menu Dosyaları

Menu dosyalarının menu_ ön eki ile isimlendirilmesine gerek yoktur. Zaten bu dosyalar menu klasörü içinde bulunmaktadır. 

####1.2.2.4 Values Dosyaları

Values kısmında bulunan dosyalar çoğul olmalıdır. 

	attrs.xml, strings.xml, styles.xml, colors.xml, dimens.xml


##2. Code Guidelines

###2.1 Java Dili Kuralları

####2.1.1 Exceptionları görmezden gelmeyin :)

Exceptionları handle etmeden bırakmayınız. 

	public void setUserId(String id) {
    	try {
        	mUserId = Integer.parseInt(id);
    	} catch (NumberFormatException e) { }
	}

Yukarıdaki yazım tarzı hem kullanıcıya, hem de uygulama geliştiriciye meydana gelen hata ile ilgili bilgi vermemektedir. Bunun yerine ilgili hata ile ilgili bilgi verdiren log yazdırılmalıdır. 

	public void setCount(String count) {
    	try {
        	count = Integer.parseInt(id);
    	} catch (NumberFormatException e) { 
    		count = 0;
        	Log.e(TAG, "There was an error parsing the count " + e);
        	DialogFactory.showErrorMessage(R.string.error_message_parsing_count);
    	}
	}

Hataları şu şekilde handle etmeliyiz. 

- Kullanıcıya hata meydana geldiğine dair uyarı vermeliyiz. 
- Hata oluşması durumunda değişkene sabit bir değer vermeliyiz. 
- Uygun olan exception'ı göstermeliyiz. 


####2.1.2 Türü belli olmayan exception'lar 


Exception yakalarken aşağıdaki şekilde en genel halini göstermemeliyiz. 


	public void openCustomTab(Context context, Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (Exception e) { 
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	}
	}

Neden?

Bu şekilde genel haliyle yakalanan exception'lar bize aldığımız hatalar hakkında bilgi vermemektedir. 


Bunun yerine duruma göre exceptionlar yakalanmaladır. 

	public void openCustomTab(Context context, Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e) { 
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	}
	}


####2.1.3 Exception'ların gruplanması. 

Exceptionlar aynı koddan meydana geliyorsa gruplandırılması gerekmektedir. 

	public void openCustomTab(Context context, @Nullable Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e) { 
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	} catch (NullPointerException e) { 
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	} catch (SomeOtherException e) { 
    		// Show some dialog
        }
	}

Gruplanmış exception' lar aşağıdaki gibidir: 

	public void openCustomTab(Context context, @Nullable Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e | NullPointerException e) { 
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	} catch (SomeOtherException e) { 
    		// Show some dialog
        }
	}


####2.1.4 Try-catch kulllanımı

Try-catch kullanımı kod okunabilirliğini arttırır. Meydana gelen hata da kolayca handle edilmiş olur. Aynı zamanda debug aşamasını da epey kolaylaştırmış olur. 

####2.1.5 Never use Finalizers

*There are no guarantees as to when a finalizer will be called, or even that it will be called at all. In most cases, you can do what you need from a finalizer with good exception handling. If you absolutely need it, define a close() method (or the like) and document exactly when that method needs to be called. See InputStreamfor an example. In this case it is appropriate but not required to print a short log message from the finalizer, as long as it is not expected to flood the logs.* - taken from the Android code style guidelines



####2.1.6 Bileşenlerin import edilmesi.

Bileşenler import edilirken tüm ismi ile import edilmeli. 

Bunun yerine:

    import android.support.v7.widget.*;

Bunu yapın :) 😃


    import android.support.v7.widget.RecyclerView;


####2.1.7 Kullanılmayan importları tutmayın. 


###2.2 Java Kod Stili Kuralları

####2.2.1 Field tanımlama ve isimlendirme

Tüm field'lar sayfanın en üstünde ve aşağıdaki kurallara göre tanımlanmalıdır.

- Private, non-static olmayan fieldların isimleri *m* ile başlamamalıdır:

    userSignedIn, userNameText, acceptButton

Aşağıdaki kullanım kod okunabilirliğini azaltmaktadır:

    mUserSignedIn, mUserNameText, mAcceptButton


- Private, static field isimleri *s* ile başlamamalıdır.:

    someStaticField, userNameText

Aşağıdaki kullanım da okunabilirliği azaltmaktadır:

	sSomeStaticField, sUserNameText


- Diğer tüm fieldlar küçük harfle başlamalıdır.


    int numOfChildren; 
    String username;


- Static final değişkenler BUYUK_HARFLE_VE_ALTYAZI ile yazılmalıdır. .

    private static final int PAGE_COUNT = 0;

Değişken isimleri kullanıma göre isimlendirilmelidir. 

    int e; //listedeki eleman sayısı

Yukarıdaki kullanım yerine, değişkenin ismini kullanım amacına göre vermemiz gerekmekdir. 

    int elemanSayisi;


####2.2.1.2 View Alanlarının İsimlendirmesi

View bileşenleri isimlendirilirken, bileşenlerinin isimlerine göre adlandırılır. 

| View           | Name               |
|----------------|--------------------|
| TextView       | usernameText       |
| Button         | acceptLoginButton  |
| ImageView      | profileAvatarImage |
| RelativeLayout | profileLayout      |

Yukarıdaki kullanım ile birlikte hangi değişkenimizin hangi bileşenden meydana geldiğini anlayabiliriz. 

####2.2.2 Container tiplerini isimlendirmede kullanmamalıyız

Bunu yapın:

    List<String> userIds = new ArrayList<>();

Bunu yapmayın:

    List<String> userIdList = new ArrayList<>();
    

####2.2.3 Aynı isimlendirme yapmaktan kaçının

Bir değişkeni, metodu isimlendirirken aynı isimleri kullanmayınız. 

	hasUserSelectedSingleProfilePreviously

	hasUserSelectedSignedProfilePreviously
	

####2.2.4 Number series naming

Method oluştururken kullandığımız parametreleri amaçlarına göre isimlendirmeliyiz. 

	public void doSomething(String s1, String s2, String s3)
	
Yukarıdaki kullanımda s1, s2 ve s3'ün ne olduğu belli değil. Bunun yerine:

	public void doSomething(String userName, String userEmail, String userId)

Parametreleri değişkenlere göre isimlendirmeliyiz. 

####2.2.5 Pronouncable names

Field' lar metotlar ve sınıflar adlandırılırken:

- Okunabilir olmalı: Etkin bir isim okunduğunda direk anlaşılabilmelidir.
- Kolay telaffuz edilebilmeli
- Kolay arama yapılabilmeli : Örneğin bir methot' u bir sınıf içerisinde ararken kolayca sonuca ulaştırabilecek isimler tercih edilmelidir. 
- Maceristan notasyonu (Hungarian notation- mLocation vb.) yukarıda bahsedilen üç maddeye uymadığı için kullanmayınız.

####2.2.6 Treat acronyms as words
####2.2.6 Kısaltmaları (Baş harflerden oluşan) kelime olarak kullanma
Sınıf isimlerinin,değişken isimlerinin kısaltmaları kelime olarak kullanıken sadece baş harfi büyük harfle yazılarak kullanılmalıdır. 
Örneğin:

| Doğru              | Yanlış           |
|--------------------|------------------|
| setUserId          |  setUserID       |
| String Uri         |  String URI      |
| int id             |  int ID          |
| parseHtml          |  parseHTML       |
| generateXmlFile    |  generateXMLFile |


####2.2.7 Satırların uzunluğuna göre ayarlama
Değişken tanımlarker satıların dizilimi için herhangi bir özel form kullanmayınız, örneğin:


    private int userId = 8;
    private int count = 0;
    private String username = "hitherejoe";

Bu şekilde yapmaktan kaçının:

    private String username = "hitherejoe";
    private int userId      = 8;
    private int count       = 0;

####2.2.8 Girintiler için boşluk kullanma

Bloklar için 4 boşluk bırakılmalı:

    if (userSignedIn) {
        count = 1;
    }

String tanımlamalar için baştan 8 satır boşluk bırakılmalı.

    String userAboutText = 
            "This is some text about the user and it is pretty long, can you see!"


###2.2.9 If-Statements

####2.2.9.1 Standart süslü parantez stili


Süslü parantez ile koşul daima aynı satırda olmalıdır.

Aşağıdaki gibi yazmaktan kaçının.

    class SomeClass {
    	private void someFunction() 
    	{
        	if (isSomething) 
        	{
            
        	} 
        	else if (!isSomethingElse) 
        	{
            
        	} 
        	else 
        	{
            
        	}
    	}
	}

yerine bunu kullanın :


	class SomeClass {
    	private void someFunction() {
        	if (isSomething) {
            
        	} else if (!isSomethingElse) {
            
        	} else {
            
        	}
    	}
	}

####2.2.9.2 Satır-içi if-clauses

bazı durumlarlar if bloklar tek satırda yazmak daha mantıklıdır. Örneğin:

    if (user == null) return false;

Fakat, bu sadece basit işlemler için geçerlidir. Aşağıdaki şekilde olan kod parçacıkları için süslü parantezlerle yazmak daha doğrudur.


    if (user == null) throw new IllegalArgumentExeption("Oops, user object is required.");
    
####2.2.9.3 İç içe if koşulu 

Mümkün oldukça iç içe if lokları yazmaktan kaçının. Örneğin:


Bunun yerine:

    if (userSignedIn) {
        if (userId != null) {
        
        }
    }
    
Bu şekilde yazmayı tercih ediniz:    
    
        if (userSignedIn && userId != null) {
    
    }
    
Bu şekilde yazımlar kod okunabilirliğini artırttırır ve gereksiz satır kullanımını engellenmiş olur.

####2.2.9.4 üçlü operatör

Uygun olduğu durumlarda işlemleri kolaylaştımak için üçlü operatörler kullanılabilir.

Örneğin bu kod satırını okumak kolaydır:


    userStatusImage = signedIn ? R.drawable.ic_tick : R.drawable.ic_cross;

ve aşağıdaki gibi fazla satır :


    if (signedIn) {
        userStatusImage = R.drawable.ic_tick;
    } else {
        userStatusImage = R.drawable.ic_cross;
    }

**Note:** Üçlü operatörlerin kullanıldığı bazı zamnlar vardır. Eğer if-koşul mantığı karmaşık ya da karakter sayısı fazla ise standart süslü parantez stili kullanılmalıdır.

###2.2.10 Annotations

####2.2.10.1 Annotation practices

Android kod stil rehberinden alınmıştır:

**@Override:** The @Override annotation must be used whenever a method overrides the declaration or implementation from a super-class. For example, if you use the @inheritdocs Javadoc tag, and derive from a class (not an interface), you must also annotate that the method @Overrides the parent class's method.

@Override annotation bir metot üst bir sınıftan kalıtıldığında kullanılmalıdır. 

**@SuppressWarnings:** The @SuppressWarnings annotation should only be used under circumstances where it is impossible to eliminate a warning. If a warning passes this "impossible to eliminate" test, the @SuppressWarnings annotation must be used, so as to ensure that all warnings reflect actual problems in the code.

--Daha fazla bilgi almak için rehbere bakabilirsiniz.
----------

Annotations mümkün oldukça her zaman kullanılmalıdır. Örneğin bir field'ın null olması umulduğu durumlarda @Nullable annotation'ı kullanılmalıdır.


    @Nullable TextView userNameText;
    
    private void getName(@Nullable String name) { }

####2.2.10.2 Annotation stili

Metotlar yada class lar için kullanılan annotation'lar satır başına sadece biri için yazılmalıdır:

    @Annotation
    @AnotherAnnotation
    public class SomeClass {
    
      @SomeAnotation
      public String getMeAString() {
      
      }
      
    }
    

####2.2.12 Kullanılmayan elementler

Bütün kullanılmayan elementler **fields**, **imports**, **methods** and **classes** kaldırılmalıdır.(Eğer özel bir sebebi yoksa).


####2.2.13 Arguments in fragments and activities

intent ve bundle ile veri gönderirken, key - value aşağıdaki tanımlamalar kullanılmalıdır.

**Activity**

Bir activity' e veri gönderirken bir KEY aracılığı ile gönderilir.


    private static final String KEY_NAME = "com.your.package.name.to.activity.KEY_NAME";

**Fragment**

Bir fragment' e veri gönderirken bir EXTRA aracılığı ile gönderilir.

    private static final String EXTRA_NAME = "EXTRA_NAME";


**Activity**

    public static Intent getStartIntent(Context context, Post post) {
        Intent intent = new Intent(context, CurrentActivity.class);
        intent.putParcelableExtra(EXTRA_POST, post);
        return intent;
    }

**Fragment**

    public static PostFragment newInstance(Post post) {
        PostFragment fragment = new PostFragment();
        Bundle args = new Bundle();
        args.putParcelable(ARGUMENT_POST, post);
        fragment.setArguments(args)
        return fragment;
    
    }
    
###2.2.23 Yorum satırları

####2.2.23.1 Satır içi yorumlar

Kodların kompleks olduğu durumlarda okuyucunun anlaması için kolay anlaşılabilir cümlelerle açıklamalar yazılmalıdır. Fakat aslolan kodların yorum satırı olmadan anlaşılabilecek şekilde yazılmasıdır.

**Not:** Yorum satırı olmak zorunda değildir max 100 karakteri de geçmemesine özen gösterilmeli.


    
### 2.3.2 Resource naming

All resource names and IDs should be written using lowercase and underscores, for example:


    text_username, activity_main, fragment_user, error_message_network_connection
    
The main reason for this is consistency, it also makes it easier to search for views within layout files when it comes to altering the contents of the file.
    
#### 2.3.2.1 ID naming

All IDs should be prefixed using the name of the element that they have been declared for. 

| Element        | Prefix    |
|----------------|-----------|
| ImageView      | image_    |
| Fragment       | fragment_ |
| RelativeLayout | layout_   |
| Button         | button_   |
| TextView       | text_     |
| View           | view_     |

For example:


    <TextView
        android:id="@+id/text_username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />


Views that typically are only one per layout, such as a toolbar, can simply be given the id of it's view type. E.g.```toolbar```.

#### 2.3.2.2 Strings

All string names should begin with a prefix for the part of the application that they are being referenced from. For example:

| Screen                | String         | Resource Name             |
|-----------------------|----------------|---------------------------|
| Registration Fragment | “Register now” | registration_register_now |
| Sign Up Activity      | “Cancel”       | sign_up_cancel            |
| Rate App Dialog       | “No thanks”    | rate_app_no_thanks        |

If it’s not possible to name the referenced like the above, we can use the following rules:

| Prefix  | Description                                  |
|---------|----------------------------------------------|
| error_  | Used for error messages                      |
| title_  | Used for dialog titles                       |
| action_ | Used for option menu actions                 |
| msg_    | Used for generic message such as in a dialog |
| label_  | Used for activity labels                     |

Two important things to note for String resources:

 - String resources should never be reused across screens. This can cause issues when it comes to changing a string for a specific screen. It saves future complications by having a single string for each screens usage.
 
 - String resources should **always** be defined in the strings file and never hardcoded in layout or class files.

#### 2.3.2.3 Styles and themes

When defining both Styles & Themes, they should be named using UpperCamelCase. For example:


    AppTheme.DarkBackground.NoActionBar
    AppTheme.LightBackground.TransparentStatusBar

    ProfileButtonStyle
    TitleTextStyle
    
    
### 2.3.3 Attributes ordering

Ordering attributes not only looks tidy but it helps to make it quicker when looking for attributes within layout files. As a general rule, 


1. View Id
2. Style
3. Layout width and layout height
4. Other `layout_` attributes, sorted alphabetically
5. Remaining attributes, sorted alphabetically

For example:

    <Button
        android:id="@id/button_accept"
        style="@style/ButtonStyle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentStart="true"
        android:padding="16dp"
        android:text="@string/button_skip_sign_in"
        android:textColor="@color/bluish_gray" />
        
Note: This formatting can be carried out by using the format feature in android studio - 

`cmd + shift + L` 

Doing this makes it easy to navigate through XML attributes when it comes to making changes to layout files.


## 2.4 Tests style rules

### 2.4.1 Unit tests

Any Unit Test classes should be written to match the name of the class that the test are targeting, followed by the Test suffix. For example:

| Class                | Test Class               |
|----------------------|--------------------------|
| DataManager          | DataManagerTest          |
| UserProfilePresenter | UserProfilePresenterTest |
| PreferencesHelper    | PreferencesHelperTest    |

All Test methods should be annotated with the `@Test` annotation, the methods should be named using the following template:


    @Test
    public void methodNamePreconditionExpectedResult() { }

So for example, if we want to check that the signUp() method with an invalid email address fails the our test would look like:


    @Test
    public void signUpWithInvalidEmailFails() { }

Tests should focus on testing only what the method name entitles, if there’s extra conditions being tested in your Test method then this should be moved to it’s own individual test.

If a class we are testing contains many different methods, then the tests should be split across multiple test classes - this helps to keep the tests more maintainable and easier to locate. For example, a DatabaseHelper class may need to be split into multiple test classes such as :


    DatabaseHelperUserTest
    DatabaseHelperPostsTest
    DatabaseHelperDraftsTest

### 2.4.2 Espresso tests

Each Espresso test class generally targets an Activity, so the name given to it should match that of the targeted Activity, again followed by Test. For example:

| Class                | Test Class               |
|----------------------|--------------------------|
| MainActivity         | MainActivityTest         |
| ProfileActivity      | ProfileActivityTest      |
| DraftsActivity       | DraftsActivityTest       |

When using the Espresso API, methods should be chained on new lines to make the statements more readable, for example:


    onView(withId(R.id.text_title))
            .perform(scrollTo())
            .check(matches(isDisplayed()))
            
Chaining calls in this style not only helps us stick to less than 100 characaters per line but it also makes it easy to read the chain of events taking place in espresso tests. 
            

# 3. Gradle Style
## 3.1 Dependencies

### 3.1.1 Versioning

Where applicable, versioning that is shared across multiple dependencies should be defined as a variable within the dependencies scope. For example:


    final SUPPORT_LIBRARY_VERSION = '23.4.0'
    
    compile "com.android.support:support-v4:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:recyclerview-v7:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:support-annotations:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:design:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:percent:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:customtabs:$SUPPORT_LIBRARY_VERSION"
    
This makes it easy to update dependencies in the future as we only need to change the version number once for multiple dependencies.

### 3.1.2 Grouping

Where applicable, dependencies should be grouped by package name, with spaces in-between the groups. For example:


    compile "com.android.support:percent:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:customtabs:$SUPPORT_LIBRARY_VERSION"
    
    compile 'io.reactivex:rxandroid:1.2.0'
    compile 'io.reactivex:rxjava:1.1.5'
    
    compile 'com.jakewharton:butterknife:7.0.1'
    compile 'com.jakewharton.timber:timber:4.1.2'
    
    compile 'com.github.bumptech.glide:glide:3.7.0'


`compile` , `testCompile` and `androidTestCompile`  dependencies should also be grouped into their corresponding section. For example:


    // App Dependencies
    compile "com.android.support:support-v4:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:recyclerview-v7:$SUPPORT_LIBRARY_VERSION"
    
    // Instrumentation test dependencies
    androidTestCompile "com.android.support:support-annotations:$SUPPORT_LIBRARY_VERSION"
    
    // Unit tests dependencies
    testCompile 'org.robolectric:robolectric:3.0'

Both of these approaches makes it easy to locate specific dependencies when required as it keeps dependency declarations both clean and tidy 🙌


### 3.1.3 Independent Dependencies

Where dependencies are only used individually for application or test purposes, be sure to only compile them using `compile` , `testCompile` or `androidTestCompile` . For example, where the robolectric dependency is only required for unit tests, it should be added using:


    testCompile 'org.robolectric:robolectric:3.0' 
