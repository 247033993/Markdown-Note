## GreenDAO使用
### 使用步骤
*  配置项目根目录的gradle文件(add部分)
<p><font color='red'>mavenCentral()</font></p>
<p><font color='red'> classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2'</font></p>
		
	```
	repositories {
        jcenter()
        mavenCentral() // add repository
    }
	```
	```
	 dependencies {
        classpath 'com.android.tools.build:gradle:2.3.3'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
    }
	```
*	配置model下得gradle文件（add部分）
	
	<p><font color='red'>apply plugin: 'org.greenrobot.greendao'</font></p>
	<p><font color='red'>compile 'org.greenrobot:greendao:3.2.2'</font></p>
	<p><font color='red'>greendao映射数据的配置</font></p>

	
	```
	apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin
	```
	```
	dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:26.+'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    compile 'org.greenrobot:greendao:3.2.2' // add library
    testCompile 'junit:junit:4.12'
	```
	```
	//add config
    greendao{
        //数据库版本号
        schemaVersion 2
        //根目录（最好这么写）
        targetGenDir 'src/main/java/'
        //包名（映射后生成一些类的所在包）
        daoPackage 'gen.dao'
    }
	```
*	编写GreenDao工具类（模板）

```
/**
 * 创建时间: 17/9/15
 * 编写人：HBB
 * 描述：GreenDao工具类
 */

public class GreenDaoUtils {
    private DevOpenHelper mHelper;
    private SQLiteDatabase db;
    private DaoMaster mDaoMaster;
    private DaoSession mDaoSession;
    public static final String DB_NAME="test.db";

    private static GreenDaoUtils greenDaoUtils;

    private GreenDaoUtils() {
    }

    public static GreenDaoUtils getSingleTon() {
        if (greenDaoUtils == null) {
            synchronized (GreenDaoUtils.class) {
                if (greenDaoUtils == null) {
                    greenDaoUtils = new GreenDaoUtils();
                }
            }
        }
        return greenDaoUtils;
    }

    private void initGreenDao() {
        //DevOpenHelper，主要是用于数据库版本更新时候的一些操作，默认是DaoMaster中的DevOpenHelper，如果要自己实现一些升级操作，需要引用自己实现的DevOpenHelper
        mHelper = new DevOpenHelper(BaseApplication.getInstance(), DB_NAME, null);
        db = mHelper.getWritableDatabase();
        mDaoMaster = new DaoMaster(db);
        mDaoSession = mDaoMaster.newSession();
    }

    public DaoSession getmDaoSession() {
        if (mDaoMaster == null) {
            initGreenDao();
        }
        return mDaoSession;
    }

    public SQLiteDatabase getDb() {
        if (db == null) {
            initGreenDao();
        }
        return db;
    }

}
```

*	编写数据库的实体类
<p>对应数据库的实体类，需要加<font color='#ff00e6'>@Entity</font>注解,如：</p>

	```
	/**
 * 创建时间: 17/9/15
 * 编写人：HBB
 * 描述：
 */
@Entity
public class Test implements Parcelable {
    @Unique
    private String name;
    private long money;


    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeString(this.name);
        dest.writeLong(this.money);
    }

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public long getMoney() {
        return this.money;
    }

    public void setMoney(long money) {
        this.money = money;
    }

    public Test() {
    }

    protected Test(Parcel in) {
        this.name = in.readString();
        this.money = in.readLong();
    }

    @Generated(hash = 1803751608)
    public Test(String name, long money) {
        this.name = name;
        this.money = money;
    }

    public static final Creator<Test> CREATOR = new Creator<Test>() {
        @Override
        public Test createFromParcel(Parcel source) {
            return new Test(source);
        }

        @Override
        public Test[] newArray(int size) {
            return new Test[size];
        }
    };
}
	
	```
	其中get、set方法在build（make project）后会自动生成，greedao各种注解说明
	
	```
@Entity：告诉GreenDao该对象为实体，只有被@Entity注释的Bean类才能被dao类操作 
@Id：对象的Id，使用Long类型作为EntityId，否则会报错。(autoincrement = true)表示主键会自增 
@Property：设置一个非默认关系映射所对应的列名，默认是使用字段名，例如：@Property(nameInDb = “name”) 
@NotNull：设置当前列不能为空 
@Transient：使用该注释的属性不会被存入数据库的字段中 
@Unique：该属性值必须是唯一值 
@Generated：编译后自动生成的构造函数、方法等的注释，提示构造函数、方法等不能被修改
	```

*	增删查改

```
//插入单条数据
GreenDaoUtils.getSingleTon().getmDaoSession().insertOrReplace(test);
```

	