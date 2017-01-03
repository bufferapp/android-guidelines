# Android项目指南
---------------------------

本文档的目的是定义Android项目指南。在整个Android项目中都应该遵循这些指南，以帮助我们保持代码的整洁和一致。这里的许多规则都将通过Jekins的代码质量检查强制执行，但是请记住没有被检查到的项目也是非常重要的 🙂。  

## 1. 项目指南

### 1.1 项目结构

在提交（contributing）代码时，整个项目应该保持以下结构：  



	src/androidTest
	src/test
	src/commonTest
	src/main


**androidTest** - 包含功能测试的目录    
**test** - 包含单元测试的目录  
**commonTest** - 包含 AndroidTest 和 Test共同使用的目录  
**main** - 包含应用程序代码的目录

任何时候你修改代码或者添加新功能，整个工程都应该保持以上已经定义好的目录结构。  
使用这样的目录结构允许我们将应用程序的代码和所有测试相关的代码分开。CommonTest允许我们在功能测试和单元测试之间共享一些类，比如：mock model creation和dagger test configuration classes。  


### 1.2 文件命名

#### 1.2.1 类文件

你在定义任意类的时候都应该使用大驼峰法命名，例如：  

	AndroidActivity, NetworkHelper, UserFragment, PerActivity


任何继承自Android framework组件的类**都应该**以组件名称结尾。例如：

	UserFragment, SignUpActivity, RateAppDialog, PushNotificationServer, NumberView

我们使用大驼峰法有助于我们将其与创建对象时使用的名称分开，并且更易阅读。类名称以框架组件名称结尾，我们可以清楚地知道这个类是用于做什么，如果你正在寻找RegistrationDialog并做一些修改，那么这种命名约定就会很容易找到这个类。  

#### 1.2.1 资源文件  

当命名资源文件时，你应该确保使用小写字母，并使用下划线来替代空格，例如：  

	activity_main, fragment_user, item_post

这个约定同样使得在查找文件时可以非常容易的定位到特定的layout文件。在Android Studio中，layout这个package是按照字母顺序排列的，这样就意味着activity，fragment以及其他的布局类型会被分组 - 从而知道从哪里开始查找一个文件。除此之外，以组件名称开头命名可以很清楚的知道这个layout文件是被什么组件/类使用。  


#### 1.2.2.1 图片文件  

图片资源应该以**ic_**前缀以及大小、颜色命名。例如：白色 24dp的接受图片应该命名为：  

	ic_accept_24dp_white

而黑色48dp的取消图标应该命名为：  

	ic_cancel_48dp_black

我们使用这样的命名约定使得图片资源根据名称就可以识别。如果颜色和尺寸没有在文件名称中指明，那么开发者需要打开图片文件才能找到这些信息。这可以为我们节约一点时间 :)    

其他的图片资源文件应该使用相应的前缀，例如：

| 类型        | 前缀    | 示例                |
|------------|-----------|------------------------|
| 选择   | selector_ | selector_button_cancel |
| 背景 | bg_       | bg_rounded_button      |
| 圆圈     | circle_   | circle_white           |
| 进度   | progress_ | progress_circle_purple |
| 分割    | divider_  | divider_grey           |

这个约定同样可以帮助我们在Android studio中按照相似项分组。同时也清楚的知道这个资源用于什么项目。例如，将一个资源命名为button_cancel可以是任意含义！这是一个选中框资源还是一个按钮的背景图片？正确的命名有助于清除可能出现的任何歧义。  

在创建选择状态资源时，也应该使用相应的后缀：

| 状态    | 后缀    | 示例             |
|----------|-----------|---------------------|
| Normal   | _normal   | btn_accept_normal   |
| Pressed  | _pressed  | btn_accept_pressed  |
| Focused  | _focused  | btn_accept_focused  |
| Disabled | _disabled | btn_accept_disabled |
| Selected | _selected | btn_accept_selected |

像上表一样使用明确的后缀可以帮助我们准确无误的知道这个资源是用于什么状态。同样使用颜色或任何其他标识符来预置资源，否则需要开发人员打开资源文件才能了解不同的选择器状态资源是什么。

#### 1.2.2.2 布局文件

在命名布局文件时，应当以使用这个布局的Android组件名称开头。比如：

| 组件        | 类名      | 布局名称       |
|------------------|-----------------|-------------------|
| Activity         | MainActivity    | activity_main     |
| Fragment         | MainFragment    | fragment_main     |
| Dialog           | RateDialog      | dialog_rate       |
| Widget           | UserProfileView | view_user_profile |
| AdapterView Item | N/A             | item_follower     |

**注意：** 如果你在创建布局时使用了merge tag，命名时应该使用`layout_` 前缀。


这种方法不仅可以方便地在目录树中查找文件，而且当需要确定布局文件所属的相应类时，它真的很有帮助。

#### 1.2.2.3 菜单文件

菜单文件不需要使用 menu_ 前缀。因为这些文件已经放在了资源目录的menu package，所以这里不需要前缀。

#### 1.2.2.4 Values 文件

所有的资源文件都应该是复数，例如：

	attrs.xml, strings.xml, styles.xml, colors.xml, dimens.xml



## 2. 编码指南

### 2.1 Java 语言规则

#### 2.1.1 不要忽略异常

避免不以正确的方式处理异常。例如：


	public void setUserId(String id) {
    	try {
        	mUserId = Integer.parseInt(id);
    	} catch (NumberFormatException e) { }
	}

这样的处理方式当发生问题时，不会给开发者和用户任何消息，使得代码调试非常困难并让用户感到困惑。当捕获到异常的时候，我们应当通过log输出异常信息以便调试，如有必要告知用户这一问题。例如：


	public void setCount(String count) {
    	try {
        	count = Integer.parseInt(id);
    	} catch (NumberFormatException e) {
    		count = 0;
        	Log.e(TAG, "There was an error parsing the count " + e);
        	DialogFactory.showErrorMessage(R.string.error_message_parsing_count);
    	}
	}

这里我们通过以下方式正确的处理异常：

- 向用户展示一个信息告知发生了一个错误
- 如果可能为变量设置一个默认值
- 抛出一个合适的异常


#### 2.1.2 从不捕获常规异常


常规异常不应该被捕获：


	public void openCustomTab(Context context, Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (Exception e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	}
	}

为什么？

*请不要这么做。在几乎所有情况下，捕获常规异常或Throwable是不合适的（最好不是Throwable，因为它包括错误异常）。这样做非常危险，因为你捕获到了一个你不希望出现（包括ClassCastException这样的RuntimeExceptions）的应用级的错误。这样会掩盖你的代码能够处理错误的属性，这意味着如果有人在你调用的代码中添加了一个异常，但是编译器不会帮助你意识到你需要对这个异常做出不同的处理。在大多数情况下，都不应该以同一种方式来处理不同类型的异常。* - 摘自Android代码指南。

相反，捕获预期的异常并做相应的处理：

	public void openCustomTab(Context context, Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	}
	}


#### 2.1.3 将异常分组

当所有的异常都执行相同的代码时，它们应该被分组以便增加可读性并避免代码重复。 例如，当可能这样做的时候：

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

你应该这样做：

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


#### 2.1.4 在抛出异常的地方使用 try-catch

在异常发生的地方使用 try-catch 语句提高代码的可读性。因为这样可以在异常发生的地方就处理掉，也使得代码调试或者修改对异常的处理变得更容易。


#### 2.1.5 从不使用 Finalizers


*对于什么时候调用finalizer，甚至finalizer是否被调用都没有保证。 在大多数情况下，您可以通过良好的异常处理将finalizer中的操作抽取出来。如果你真的需要，定义一个close（）方法（或者类似的方法），并在文档中准确的说明什么时候该方法应该被调用。可以将InputStreamfor作为一个范例参考。在这种情况下，在finalizer中打印一个简短的log信息是合适的但不是必须的，因为不希望log被淹没。* - 摘自Android代码指南。



#### 2.1.6 完全限定imports

当声明一个import的时候使用完整的package定义。例如：

不要这样做：


    import android.support.v7.widget.*;

相反，这样做 😃


    import android.support.v7.widget.RecyclerView;


#### 2.1.7 不要保留没有使用的imports

有时从一个类中删除代码会导致一些import不再需要。如果是这种情况，相应的import应该和代码一起移除。

### 2.2 Java 风格规则

#### 2.2.1 成员的定义和命名

所有的成员都应该在文件的顶部声明，按照如下规则：


- 私有的非静态变量名称不应该以m开头。这样是正确的：

    userSignedIn, userNameText, acceptButton

而不是这样：

    mUserSignedIn, mUserNameText, mAcceptButton


- 私有的静态变量不应该以s开头。这样是正确的：

    someStaticField, userNameText

而不是这样：

	sSomeStaticField, sUserNameText


- 所有其他的成员都应该以小写字母开头


    int numOfChildren;
    String username;


- 静态最终变量（即常量）应该全部大写且以下划线连接


    private static final int PAGE_COUNT = 0;

不应使用没有明显含义的成员变量名称。如：

    int e; //number of elements in the list

为什么要通过注释而不是一开始就给取一个有意义的名称呢？

    int numberOfElements;

这样就好多了！


#### 2.2.1.2 View 成员变量命名

当为一个指向view的成员命名时，成员名称的最后一个单词应该是view的名称。例如：

| View类型           | 名称              |
|----------------|-------------------|
| TextView       | usernameView      |
| Button         | acceptLoginView   |
| ImageView      | profileAvatarView |
| RelativeLayout | profileLayout     |

我们以这样的方式命名，可以轻松的确定该成员变量所对应的内容。例如，假设有一个成员变量名字为**user**则是非常模糊的 - 但是给它一个usernameView, userAvatarView or userProfieLayout这样的名称则可以帮助我们清楚的确定它所对应的内容。

以前，view成员名称常常以view的类型结尾（如：acceptLoginButton），但是常常修改view的类型但是很容易忘记返回java文件更新成员变量的名称。

#### 2.2.2 避免在命名时使用容器类型

继续前面的主题，当我们在为一个集合变量命名时，应该避免使用容器的类型。比如，我们有一个arraylist包含了一个userIds列表：

请这样做：

    List<String> userIds = new ArrayList<>();

而不是：

    List<String> userIdList = new ArrayList<>();

和前面的view命名一样，当以后容器的类型发生变化时，常常会忘记修改变量的名称，所有完全没有必要。准确的为容器本身命名需要提供足够的信息说明它是什么。


#### 2.2.3 避免使用相似的名称

给变量，方法以及/或者类取相似的名称将让其他开发者在阅读你的代码时感到困惑。比如：

	hasUserSelectedSingleProfilePreviously

	hasUserSelectedSignedProfilePreviously

这样的命名很难在第一眼就分清这两者谁是谁。以更清晰的方式命名这些变量、方法会让开发者更容易在你的代码之间跳转。

#### 2.2.4 数字系列命名

当Android Studio为我们自动生成代码的时候，总是很容易保留代码原来的样子 - 即使它为我们生成了可怕的参数名称！例如，这个就不是很好：

	public void doSomething(String s1, String s2, String s3)

如果不读源码就很难理解这些参数。取而代之：

	public void doSomething(String userName, String userEmail, String userId)

这样就很容易理解！现在我们就能在随着参数阅读代码进而更清晰的理解代码🙂

#### 2.2.5 可以发音的名称

当为成员变量，方法以及类命名是，它们应该是：


- 可读：有效的命名意味着我们看到这个名称时就能立即理解它，减少试图破译名称含义的负担。

- 可说：可说的名称可以避免在对话中尝试说出一个不好的名称而引起尴尬

- 可搜索：没有什么比尝试在类中搜索一个方法或变量时发现其被错误拼写或者不规则命名更糟糕。当我们尝试查找一个搜索用户的函数时，然后搜索 ‘search’应该有一个该函数的结果。

- 不使用匈牙利语符号：匈牙利符号违反了上述三点，因此不应该使用！


#### 2.2.6 将缩略词作为词语使用Treat acronyms as words


任何类名，变量名等的首字母缩写应被视为单词 - 这适用于任何字母使用的任何大写。 例如：

| Do              | Don't           |
|-----------------|-----------------|
| setUserId       | setUserID       |
| String uri      | String URI      |
| int id          | int ID          |
| parseHtml       | parseHTML       |
| generateXmlFile | generateXMLFile |


#### 2.2.7 避免调整变量声明

任何声明都不应该使用特殊形式的对齐，例如：

这样比较好：

    private int userId = 8;
    private int count = 0;
    private String username = "hitherejoe";

避免这样做：

    private String username = "hitherejoe";
    private int userId      = 8;
    private int count       = 0;

这样会产生空白流从而使得阅读非常困难。

#### 2.2.8 使用空格缩进


代码块应该使用4个空格缩进：


    if (userSignedIn) {
        count = 1;
    }

而对于换行，应该使用8个空格缩进：



    String userAboutText =
            "This is some text about the user and it is pretty long, can you see!"


### 2.2.9 If-语句

#### 2.2.9.1 使用标准的括号风格

大括弧应该和代码的前一行在一起。例如，应该避免这样书写：


    class SomeClass
    {
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

应该这样：


	class SomeClass {
    	private void someFunction() {
        	if (isSomething) {

        	} else if (!isSomethingElse) {

        	} else {

        	}
    	}
	}

不仅仅是因为空间上不需要另起一行，这样会使得在阅读代码时更容易跟上代码。

#### 2.2.9.2 内联 if-分句

有时仅用一行完成if语句是有意义的，比如：

    if (user == null) return false;

然而，单行仅仅针对一些简单的操作有效。有时像这样的语句还是使用括号包起来比较好：


    if (user == null) throw new IllegalArgumentExeption("Oops, user object is required.");

#### 2.2.9.3 嵌套 if 条件

在可能的情况下，if语句都应该组合使用，以避免过度复杂的if嵌套。比如：

这样组合：


    if (userSignedIn && userId != null) {

    }

尽量避免：


    if (userSignedIn) {
        if (userId != null) {

        }
    }

这样使得语句更容易阅读，而且还将不必要的额外行从嵌套子句中移除

#### 2.2.9.4 三元运算符

在适当的情况下，可以使用三元运算符来简化操作。

例如，这样就比较易读：


    userStatusImage = signedIn ? R.drawable.ic_tick : R.drawable.ic_cross;

并且比这样少占几行代码：


    if (signedIn) {
        userStatusImage = R.drawable.ic_tick;
    } else {
        userStatusImage = R.drawable.ic_cross;
    }

**注意：** 有时三元运算符并不适合使用。如果 if语句的逻辑很复杂，或者是很多字符那么应该使用标准的括号风格。

### 2.2.10 注解

#### 2.2.10.1 注解实践

摘自Android代码指南：

**@Override：** 任何时候当一个方法重载或者实现了父类的声明时，就需要使用@Override这个注解。例如：如果你使用了@inheritdocs这个javadoc tag并且继承自一个类（不是一个接口），你就必须声明这个方法@Override了父类的方法。


**@SuppressWarnings：** @SuppressWarnings注解仅仅在无法消除警告时才可以使用。如果一个警告能够通过“无法消除”的测试，@SuppressWarnings注解就应该使用，这样就可以保证所有的警告都能够反应代码中的真实问题。

更多有关注解指南请参考这里。

----------

在可能的情况下，注解都可以使用。比如，当一个变量可能为null时就可以使用@Nullable注解。例如：


    @Nullable TextView userNameText;

    private void getName(@Nullable String name) { }

#### 2.2.10.2 注解风格

注解应用于方法或者类时应该是在声明时使用，一行只能使用一个注解：


    @Annotation
    @AnotherAnnotation
    public class SomeClass {

      @SomeAnotation
      public String getMeAString() {

      }

    }

当注解用于变量时，只要有空间，你可以将注解和变量保持在同一行，例如：


    @Bind(R.id.layout_coordinator) CoordinatorLayout coordinatorLayout;


    @Inject MainPresenter mainPresenter;


我们这样做是的语句更易读。比如这个语句‘@Inject SomeComponent mSomeName’ 就可以理解为 ‘注入component为name’。

#### 2.2.11 限制变量的范围

局部变量的范围应该控制到最小（Effective Java 第29条）。这样做可以提高代码的可读性和可维护性，并减少出错的可能性。 每个变量应该在包含变量所有使用的最内层块中声明。

局部变量的应该在第一次使用时声明。几乎每一个局部变量在声明时都应该包含一个初始化。如果还没有足够的信息来初始化一个变量，那么你应该推迟到有足够的信息初始化时再声明这个变量 - 摘自Android代码风格指南。


#### 2.2.12 未使用的元素

所有未使用的**变量**，**imports**，**函数** 和 **类**都应该被从代码中移除，除非有特殊的原因需要保留。

#### 2.2.13 Import 语句排序

因为我们使用Android Studio，所以import总是自动排序。然而，在没有自动排序的情况下，它们应该按照如下顺序排序：


1. Android imports
2. Imports from third parties
3. java and javax imports
4. Imports from the current Project

**注意:**

- 在每一组内的Imports应该以字母顺序排列，大写字母排在小写字母的前面（如：Z应该在a的前面）
- 在每一个大的分组（android, com, JUnit, net, org, java, javax）之间应该有一个空行

#### 2.2.14 日志输出

日志输出应该用于在开发阶段打印有用的错误信息和/或其他或许有用的信息


| Log                               | Reason      |
|-----------------------------------|-------------|
| Log.v(String tag, String message) | verbose     |
| Log.d(String tag, String message) | debug       |
| Log.i(String tag, String message) | information |
| Log.w(String tag, String message) | warning     |
| Log.e(String tag, String message) | error       |


我们可以在一个类的顶部设置 一个`static final`的`Tag` 用于log输出，例如：


    private static final String TAG = MyActivity.class.getName();

所有的verbose 和 debug log在release版本中都应该禁用。另一方面 - 信息，警告和错误日志只能在必要时保持启用。


    if (BuildConfig.DEBUG) {
        Log.d(TAG, "Here's a log message");
    }

**注意:** Timber 是值得使用的log工具. 它帮我们处理Tagging，减少了我们对tag的引用.

#### 2.2.15 变量顺序

在类文件顶部声明的任意变量都应该按照如下顺序排列：

1. Enums
2. Constants
3. Dagger Injected fields
4. Butterknife View Bindings
5. private global variables
6. public global variables

例如:

	public static enum {
		ENUM_ONE, ENUM_TWO
	}

	public static final String KEY_NAME = "KEY_NAME";
	public static final int COUNT_USER = 0;

	@Inject SomeAdapter someAdapter;

	@BindView(R.id.text_name) TextView nameText;
	@BindView(R.id.image_photo) ImageView photoImage;

	private int userCount;
	private String errorMessage;

	public int someCount;
	public String someString;


使用这样的约定帮助我们保持变量声明的分组，分组可以增加变量的可读性和可定位性

#### 2.2.16 类成员排序


为了提高代码的可读性，按照一定的逻辑方式组织类的成员非常重要。应该按照以下排列顺序来到达这一目的：


1. 常量
2. 成员变量
3. 构造函数
4. 重载方法和回调函数 (公开的或者私有的)
5. 公开方法
6. 私有方法
7. 内部类或者接口

例如:


    public class MainActivity extends Activity {

        private int count;

        public static newInstance() { }

        @Override
        public void onCreate() { }

        public setUsername() { }

        private void setupUsername() { }

        static class AnInnerClass { }

        interface SomeInterface { }

    }

任意Android framework类中使用的生命周期函数都应该按照相应的生命周期排序。例如：


    public class MainActivity extends Activity {

        // Field and constructors

        @Override
        public void onCreate() { }

        @Override
        public void onStart() { }

        @Override
        public void onResume() { }

        @Override
        public void onPause() { }

        @Override
        public void onStop() { }

        @Override
        public void onRestart() { }

        @Override
        public void onDestroy() { }

        // public methods, private methods, inner classes and interfaces

    }

#### 2.2.17 函数参数排序

在定义函数时，参数的排序应该按照如下约定：

    public Post loadPost(Context context, int postId);


    public void loadPost(Context context, int postId, Callback callback);

**Context** 参数总是第一个 **Callback** 参数总是最后一个.

#### 2.2.18 字符串常量的命名和取值

在使用字符串常量时，它们应该被声明为 final static 并遵循如下约定：

[Strings table]

#### 2.2.19 枚举

枚举仅在必须使用时才使用。如果有可能使用其他方法，那么尽量使用其他方法实现。例如：

替换这样的方案：


    public enum SomeEnum {
        ONE, TWO, THREE
    }

采用这样的方法：

    private static final int VALUE_ONE = 1;
    private static final int VALUE_TWO = 2;
    private static final int VALUE_THREE = 3;

#### 2.2.20 fragments 和 activities之间的参数

当使用Intent或者Bundle传递数据时，这些值得key定义应该遵循以下约定：

**Activity**

向一个Activity传递数据时必须使用一个KEY的引用，KEY的定义如下：


    private static final String KEY_NAME = "com.your.package.name.to.activity.KEY_NAME";

**Fragment**

向fragment传递数据时必须使用一个EXTRA的引用，EXTRA的定义如下：


    private static final String EXTRA_NAME = "EXTRA_NAME";

当需要创建一个fragment 或者activity新的实例时，我们必须提供一个静态的方法来产生返回新的实例，将需要传递的数据作为这个函数的参数。例如：

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

#### 2.2.21 行长度限制

代码行的长度不应该超过100个字符，这样会是的代码更加可读。有时为了达到这一目的，我么可能需要：


- 将数据抽象为局部变量
- 将逻辑抽象为一个外部函数
- 将单行代码换行成几行

**注意:** 对于代码注释和import语句，单行可以超过100个字符的限制  

#### 2.2.21.1 换行技巧

当需要换行的时候，在这些情况下我们在代码格式化方面需要保持一致。

**在操作符的地方换行**

当需要在操作符的地方换行时，我们在操作符前面换行：


    int count = countOne + countTwo - countThree + countFour * countFive - countSix
            + countOnANewLineBecauseItsTooLong;

如果需要，你也可以在 `=` 符号后面换行：


    int count =
            countOne + countTwo - countThree + countFour * countFive + countSix;

**函数链**

当涉及函数链的时候，每一个函数调用都应该在新的一行：

不要这样：


    Picasso.with(context).load("someUrl").into(imageView);

相反，应该这样：


    Picasso.with(context)
            .load("someUrl")
            .into(imageView);

**长参数**

当一个函数包含很长的参数时，我们需要在适当的地方换行。例如，当声明了这样一个方法时，我们应该在合适的参数逗号后面换行：


    private void someMethod(Context context, String someLongStringName, String text,
                                long thisIsALong, String anotherString) {               
    }             

然后在调用这个函数时，我们应该在每一个参数的逗号后面换行：


    someMethod(context,
            "thisIsSomeLongTextItsQuiteLongIsntIt",
            "someText",
            01223892365463456,
            "thisIsSomeLongTextItsQuiteLongIsntIt");


#### 2.2.22 函数空格

在类中的函数之间仅需要一行分割，例如：

即这样：


    public String getUserName() {
        // Code
    }

    public void setUserName(String name) {
        // Code
    }

    public boolean isUserSignedIn() {
        // Code
    }

而不是这样：


    public String getUserName() {
        // Code
    }


    public void setUserName(String name) {
        // Code
    }


    public boolean isUserSignedIn() {
        // Code
    }

### 2.2.23 注释

#### 2.2.23.1 行内注释

在需要时，可在特定的代码处提供有意义的单行注释让阅读者理解代码的含义。单行注释仅仅适用于代码很难理解的情况。然而，在大多数情况下代码应该写到没有注释也能理解🙂。

**注意:** 不是必须，但是应该尽量将代码注释也坚持100个字符原则。

#### 2.2.23.2 Java文档样式注释


虽然函数名称通常足以表达函数的功能，但有时JavaDoc样式的注释也可以提供帮助。 这有助于读者容易地理解函数的功能，以及传递到方法中的任何参数的目的。


    /**
     * Authenticates the user against the API given a User id.
     * If successful, this returns a success result
     *
     * @param userId The user id of the user that is to be authenticated.
     */

#### 2.2.23.3 类注释

当创建类注释时应该有意义的且是描述性的，当有必要时使用链接。例如：


    /**
      * RecyclerView adapter to display a list of {@link Post}.
      * Currently used with {@link PostRecycler} to show the list of Post items.
      */

不要保留作者注释，当多个人都在这个类上工作时，这样的信息是无用的且不能提供任何有意义的信息。


    /**
      * Created By Joe 18/06/2016
      */

### 2.2.24 分割代码

#### 2.2.24.1 Java 代码

如果为代码创建‘分割线’，应该使用如下的方法，像这样：


    public void method() { }

    public void someOtherMethod() { }

    /********* Mvp Method Implementations  ********/

    public void anotherMethod() { }

    /********* Helper Methods  ********/

    public void someMethod() { }

而不是这样：


    public void method() { }

    public void someOtherMethod() { }

    // Mvp Method Implementations

    public void anotherMethod() { }

这样是的在类里更容易定位分割后的方法。

#### 2.2.24.2 字符串文件

string.xml文件中定义的字符串资源应该按照功能分组，例如：


    // User Profile Activity
    <string name="button_save">Save</string>
    <string name="button_cancel">Cancel</string>

    // Settings Activity
    <string name="message_instructions">...</string>

这样做不仅仅是保持字符串整齐，当字符串需要修改的时候会很容易找到相关的字符串。

#### 2.2.24.3 RxJava 链

在使用Rx链式操作时，每一个操作都应该独占一行，在`.`号前面换行。例如：

    return dataManager.getPost()
                .concatMap(new Func1<Post, Observable<? extends Post>>() {
                    @Override
                     public Observable<? extends Post> call(Post post) {
                         return mRetrofitService.getPost(post.id);
                     }
                })
                .retry(new Func2<Integer, Throwable, Boolean>() {
                     @Override
                     public Boolean call(Integer numRetries, Throwable throwable) {
                         return throwable instanceof RetrofitError;
                     }
                });

这样可以更容易理解Rx链调用的操作流程。

### 2.2.25 Butterknife

#### 2.2.25.1 事件监听器

尽可能的使用Butterknife的监听绑定。例如，当需要监听一个点击事件时，不要这样做：


    mSubmitButton.setOnClickListener(new View.OnClickListener() {
        public void onClick(View v) {
            // Some code here...
        }
      };

请这样做：


    @OnClick(R.id.button_submit)
    public void onSubmitButtonClick() { }


## 2.3 XML 样式规则

### 2.3.1 使用自结束（self=-closing）标记

如果XML layout中的view没有子view，应该使用自结束标记。

即：


    <ImageView
        android:id="@+id/image_user"
        android:layout_width="90dp"
        android:layout_height="90dp" />

而不是：


    <ImageView
        android:id="@+id/image_user"
        android:layout_width="90dp"
        android:layout_height="90dp">
    </ImageView>


### 2.3.2 资源命名

所有的资源名称以及ID都应该使用小写和下划线命名，例如：


    text_username, activity_main, fragment_user, error_message_network_connection

这样做得主要原因是一致性，同是也使得当需要修改文件内容时更容易在文件中搜索view。

#### 2.3.2.1 ID 命名

所有的ID都应该以其声明的元素名称作为前缀。

| 元素        | 前缀    |
|----------------|-----------|
| ImageView      | image_    |
| Fragment       | fragment_ |
| RelativeLayout | layout_   |
| Button         | button_   |
| TextView       | text_     |
| View           | view_     |

例如：


    <TextView
        android:id="@+id/text_username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />


对于典型的每个layout中只会存在一个的view，可以简单的将其id命名为view类型。比如：```toolbar```。

#### 2.3.2.2 字符串

所有的字符串名称都应该以应用中使用它的模块作为前缀。例如：

| 界面                | 字符串         | 资源名称             |
|-----------------------|----------------|---------------------------|
| Registration Fragment | “Register now” | registration_register_now |
| Sign Up Activity      | “Cancel”       | sign_up_cancel            |
| Rate App Dialog       | “No thanks”    | rate_app_no_thanks        |

如果无法按照上面的规则命名，我们需要遵循如下规则：

| 前缀  | 描述                                  |
|---------|----------------------------------------------|
| error_  | 用于错误信息                      |
| title_  | 用于对话框标题                      |
| action_ | 用于选项菜单的动作                 |
| msg_    | 用于常规消息，比如对话框展示 |
| label_  | 用于 activity 标签                    |

字符串资源有两件重要的事情需要注意：

 - 字符串资源决不允许在多个界面之间重复使用。否则为一个界面修改字符其他界面就会出现问题。通过每一个界面单独使用一个字符串可以避免将来出现难题。

 - 字符串资源**总是**定义在字符串资源文件中，决不允许在layout文件或者类文件中硬编码字符。

#### 2.3.2.3 样式和主题

在定义样式和主题时，应该使用大驼峰规则。例如：


    AppTheme.DarkBackground.NoActionBar
    AppTheme.LightBackground.TransparentStatusBar

    ProfileButtonStyle
    TitleTextStyle


### 2.3.3 属性顺序

为属性排序不仅仅是看起来整洁，还能够帮助我们更快的在layout文件中查看属性。通用的规则是：


1. View Id
2. 样式
3. 布局宽度、高度
4. 其他 `layout_` 属性, 按字母排序
5. 保留属性, 按字母排序

例如:

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

注意：这种格式可以通过android studio的格式化功能实现 - 

`cmd + shift + L`

这样做使得当需要修改layout文件时更容易在XML属性之间跳转。


## 2.4 测试风格规则

### 2.4.1 单元测试

所有的单元测试类都应该和被测试的目标类匹配，以Test为前缀。例如：

| 类                | 测试类               |
|----------------------|--------------------------|
| DataManager          | DataManagerTest          |
| UserProfilePresenter | UserProfilePresenterTest |
| PreferencesHelper    | PreferencesHelperTest    |

所有的测试函数都应该注以`@Test` 注解，这些函数应该按照如下模板命名：


    @Test
    public void methodNamePreconditionExpectedResult() { }

例如，如果我们想要用一个无效的email地址来检查signup()函数会失败，测试函数应该像这样：


    @Test
    public void signUpWithInvalidEmailFails() { }

测试应该只关注测试方法名称所赋予的权限，如果在测试方法中测试了额外的条件，那么应该移动到它自己的单元测试。

如果一个被测试的类包含许多不同的函数，那么这些测试应该拆分成多个测试类 - 这将使得测试类更加可维护和更易定位，一个DatabaseHelper类也需要拆分成多个测试类，比如这样：


    DatabaseHelperUserTest
    DatabaseHelperPostsTest
    DatabaseHelperDraftsTest

### 2.4.2 Espresso 测试

通常每一个Espresso测试应用于一个Activity，所以它的命名应该和它的测试目标Activity匹配，同样需要以Test结尾。例如：

| 类                | 测试类               |
|----------------------|--------------------------|
| MainActivity         | MainActivityTest         |
| ProfileActivity      | ProfileActivityTest      |
| DraftsActivity       | DraftsActivityTest       |

当使用Espresso API时，函数链中的函数应该另起一行以保持代码更加可读，例如：


    onView(withId(R.id.text_title))
            .perform(scrollTo())
            .check(matches(isDisplayed()))

以这样的规则链式调用函数不仅仅帮助我们坚持了一行少于100字符原则，还使得更容易阅读espresso测试中所发生的事情。


# 3. Gradle 风格
## 3.1 依赖

### 3.1.1 版本

如有可能，多个依赖之间共同使用的版本号应该定义成一个变量。例如：


    final SUPPORT_LIBRARY_VERSION = '23.4.0'

    compile "com.android.support:support-v4:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:recyclerview-v7:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:support-annotations:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:design:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:percent:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:customtabs:$SUPPORT_LIBRARY_VERSION"

这样使得以后在更新依赖时仅仅需要修改一个版本号即可。

### 3.1.2 分组

如有可能，依赖应该按照包名分组，各组之间插入一个空行。例如：


    compile "com.android.support:percent:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:customtabs:$SUPPORT_LIBRARY_VERSION"

    compile 'io.reactivex:rxandroid:1.2.0'
    compile 'io.reactivex:rxjava:1.1.5'

    compile 'com.jakewharton:butterknife:7.0.1'
    compile 'com.jakewharton.timber:timber:4.1.2'

    compile 'com.github.bumptech.glide:glide:3.7.0'


`compile` , `testCompile` 和 `androidTestCompile`  依赖应该按照相应的区域分组。例如： 


    // App Dependencies
    compile "com.android.support:support-v4:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:recyclerview-v7:$SUPPORT_LIBRARY_VERSION"

    // Instrumentation test dependencies
    androidTestCompile "com.android.support:support-annotations:$SUPPORT_LIBRARY_VERSION"

    // Unit tests dependencies
    testCompile 'org.robolectric:robolectric:3.0'

所有这些方法都是为了后续有需要时可以更容易定位特定的依赖，同时还能保证依赖的声明整洁。 🙌


### 3.1.3 独立依赖

当某些依赖仅仅单独用于应用或者测试目的时，确保在编译他们时使用了`compile` , `testCompile` 或者 `androidTestCompile`。例如，robolectric依赖仅仅用于单元测试，那么应该这样添加：


    testCompile 'org.robolectric:robolectric:3.0'
