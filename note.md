
Qsb的全局变量mSourceTaskExecutor类似管理所有查询Executor的入口，而每一个searchable source都有对应的一个SingleThreadNamedTaskExecutor来执行真正的查询任务。class PerNameExecutor管理所有的searchable sources的executor，class BatchingNamedTaskExecutor管理某一次查询的任务队列)。

/home/qloop/packages/apps/QuickSearchBox/src/com/android/quicksearchbox


## 查看本机存在的jdk版本
sudo update-alternatives --config java

##安装openjdk7
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get -f install
sudo apt-get install openjdk-7-jdk

---
##8.1

//判断应用是否在运行 
ActivityManager am = (ActivityManager)context.getSystemService(Context.ACTIVITY_SERVICE);
List<RunningTaskInfo> list = am.getRunningTasks(100);  //参数为最大线程数
boolean isAppRunning = false;
String MY_PKG_NAME = "com.cyberblue.iitag";
for (RunningTaskInfo info : list) {
	if (info.topActivity.getPackageName().equals(MY_PKG_NAME) || info.baseActivity.getPackageName().equals(MY_PKG_NAME)) {
		isAppRunning = true;
		Log.i(TAG,info.topActivity.getPackageName() + " info.baseActivity.getPackageName()="+info.baseActivity.getPackageName());
		break;
	}
}


//编译出错
build/core/host_shared_library_internal.mk:51: recipe for target 'out/host/linux-x86/obj/lib/libart.so' failed
解决方法:
clang编译器导致的问题，目前可行的修改方法时在art/build/Android.common_build.mk ,中将host 默认编辑器使用clang关掉，找到WITHOUT_HOST_CLANG

关闭clang即可
# Host.
ART_HOST_CLANG := false
ifneq ($(WITHOUT_HOST_CLANG),true)
  # By default, host builds use clang for better warnings.

  ART_HOST_CLANG := false
endif

---
## 8.2
- handler.removeCallbacks是将MessageQueue中的runnable对象移除,使线程对象停止运行
- Filed fild = android.support.v17.leanback.app.SearchFragment.class.getDeclaredField("mSearchBar");
  fild.setAccessible(true);//取消java的访问检查，提高反射的速度
- Activity的四种启动模式：

    1. standard

        默认启动模式，每次激活Activity时都会创建Activity，并放入任务栈中。

    2. singleTop

        如果在任务的栈顶正好存在该Activity的实例， 就重用该实例，否者就会创建新的实例并放入栈顶(即使栈中已经存在该Activity实例，只要不在栈顶，都会创建实例)。

    3. singleTask

        如果在栈中已经有该Activity的实例，就重用该实例(会调用实例的onNewIntent())。重用时，会让该实例回到栈顶，因此在它上面的实例将会被移除栈。如果栈中不存在该实例，将会创建新的实例放入栈中。 

    4. singleInstance

        在一个新栈中创建该Activity实例，并让多个应用共享改栈中的该Activity实例。一旦改模式的Activity的实例存在于某个栈中，任何应用再激活改Activity时都会重用该栈中的实例，其效果相当于多个应用程序共享一个应用，不管谁激活该Activity都会进入同一个应用中。

    > 一个应用的Activity供多种方式调用启动的情况，多个调用希望只有一个Activity的实例存在，这就需要Activity的onNewIntent(Intent intent)方法.   lanuchMode=“singleTask”
    > Activity第一启动的时候执行onCreate()---->onStart()---->onResume()等后续生命周期函数，也就时说第一次启动Activity并不会执行到onNewIntent(). 而后面如果再有想启动Activity的时候，那就是执行onNewIntent()---->onResart()------>onStart()----->onResume().  如果android系统由于内存不足把已存在Activity释放掉了，那么再次调用的时候会重新启动Activity即执行onCreate()---->onStart()---->onResume()
    PS : 当调用到onNewIntent(intent)的时候，需要在onNewIntent() 中使用setIntent(intent)赋值给Activity的Intent.否则，后续的getIntent()都是得到老的Intent。	


---

## 8.3
- AndroidStudio的全局关键词搜索:  ctrl+shift+f

- Invocationtargetexpection
- UiObjectNotFoundExpection
- UiSelector 无匹配

 > Caused by: com.android.uiautomator.core.UiObjectNotFoundException: UiSelector[CLASS=android.widget.FrameLayout, RESOURCE_ID=com.google.android.leanbacklauncher:id/orb]

- /home/qloop/adt-bundle-linux-x86_64-20140702/sdk/sources/android-21/android/support/v17/leanback/widget/SearchBar.java

---

## 8.4
- [SearchFragment]// update ui
        if (movieList.size() != 0) {
          uiShowQuery = mSuggestions.getQuery();
          int index = mRowsAdapter.size();
          ArrayObjectAdapter listRowAdapter = new ArrayObjectAdapter(
              gameCardPresenter);
          HeaderItem header = new HeaderItem(index, appName);
          if ("银河影视".equals(appName)) {
            listRowAdapter = new ArrayObjectAdapter(
                videoCardPresenter);
          }
          for (int j = 0; j < movieList.size(); j++) {
            listRowAdapter.add(movieList.get(j));
          }
          mRowsAdapter.add(new ListRow(header, listRowAdapter));
          mRowsAdapter.notifyArrayItemRangeChanged(0,
              mRowsAdapter.size());
        }
- HeaderItem
- ObjectAdapter-> ArrayObjectAdapter
- Row
-  adb push out/debug/target/product/t210/system/priv-app/QuickSearchBox/QuickSearchBox.apk  /system/priv-app/QuickSearchBox/

---
## 8.5
- 编译 :
  export TOP=`pwd`
  . build/envsetup.sh
  setpaths
  choosecombo 2 darcy_ironfist 2
  export BUILDBRAIN_NAME_ID=40018
  export GIT_SYNC_REVISION=6546666
  export BUILD_NUMBER=${BUILDBRAIN_NAME_ID}_$(echo ${GIT_SYNC_REVISION} | sed -r "s/([0-9]{4})$/.\1/")
mp dev
- ubuntu反编译jar:
jd-GUI
download Url : http://www.xuebuyuan.com/2041100.html
error resolve: sudo apt-get install libgtk2.0-0:i386 libxxf86vm1:i386 libsm6:i386 lib32stdc++6

- 提交代码:
  1. git status 查看当前状态
  2. git diff  修改红色改动处
  3. git add '提交文件'
  4. git commit -m "提交描述"
  5. git push origin HEAD:refs/for/imaster

- 检测当前的网络连接:
  private boolean checkNetworkAvailable() {
        ConnectivityManager connectivity = (ConnectivityManager) this
                .getSystemService(Context.CONNECTIVITY_SERVICE);
        if (connectivity == null) {
            return false;
        } else {
            NetworkInfo[] info = connectivity.getAllNetworkInfo();
            if (info != null) {
                for (int i = 0; i < info.length; i++) {
                    if (info[i].getState() == NetworkInfo.State.CONNECTED) {
                        NetworkInfo netWorkInfo = info[i];
                        if (netWorkInfo.getType() == ConnectivityManager.TYPE_WIFI) {
                            return true;
                        } else if (netWorkInfo.getType() == ConnectivityManager.TYPE_MOBILE) {
                            return true;
                        } else if (netWorkInfo.getType() == ConnectivityManager.TYPE_ETHERNET) {
                            return true;
                        }
                    }
                }
            }
        }

        return false;
    }
    
- 跳回顶部: 
  public void jumpToTopRow() {
    Field fild = null;
    try {
      fild = android.support.v17.leanback.app.SearchFragment.class.getDeclaredField("mRowsFragment");
      fild.setAccessible(true);
      RowsFragment rowsFragment = (RowsFragment) fild.get(mFragment);
      Method getVerGridView = Class.forName("android.support.v17.leanback.app.BaseRowFragment").getDeclaredMethod("getVerticalGridView");
      getVerGridView.setAccessible(true);
      VerticalGridView verGridView = (VerticalGridView)getVerGridView.invoke(rowsFragment);
//      verGridView.setSelectedPosition(0);
      verGridView.setSelectedPositionSmooth(0);
    } catch (Exception e) {
      Log.e(TAG, "mRowsFragment Cannot be found ", e);
    }
    }

- git format-patch -2
- git reset 'id'
- git log
- git commit --amend   #修改已经提交的log

---

## 8.8

- Bug  169712 & Bug 151386 待确认 A键是否与R2同时具有搜索功能 ？bug 169712不存在 : 修复151386后169712就不会出现
- git add时意外关机导致git push时error: object empty。
  解决： 1. git log 查看记录
        2. git reset ‘commit id’ 回退到上一版本，然后重新add push

---

## 8.9

- DeadObjectException: 调用对象不存在，因为其所在进程已被杀死

- adb shell  -> am(activity manager) kill <packagename>/ am force-stop <packagename> #强制关闭packname的程序


- getQsbApplication().onStartupComplete();

---

## 8.10[周三]

- "The onLowMemory() thing is not for this purpose.  In fact this may not have  anything to do with memory at all -- if a process you have a cursor on crashes for whatever reason, your process needs to be killed as well. "

- 由qsb启动的 gitv和nv的进程，不论是否搜索，只要在qsb中stop掉gitv或者是nv的进程，就会qsb也会一块kill
- 不是由qsb启动的gitv和nv的进程,只有在搜索之后（即持有了Cursor之后） 才会连带被Kill

---

## 8.11[周四]

- loadRows suggestions is done:true
D iQiyiSearchFragment: next corpusResult:com.tvos.gamestore/org.cocos2dx.lua.AppActivity[n]

D iQiyiSearchFragment: next corpusResult:com.nvidia.tegrazone3/com.nvidia.tegrazone.search.searchsuggest.SearchActivity[n]

D iQiyiSearchFragment: next corpusResult:com.gitvvideo/com.qiyi.video.project.configs.tvos.gamebox.common.androidTV.MainActivity[n]

- mkdir -p ***： 建立上层目录 eg. /root/test/test   mkdir -p ~/root/test/test
- curl  下载

---

## 8.12 [周五]

- lowmemorykiller
- 查看应用内存情况: adb shell dumpsys meminfo '包名'
- git fetch <远程主机名 eg.origin> <分支名 可以不写默认取回全部分支>获取远程更新（没有和本地分支合并）
- git merge origin/master <主机/分支> : 在本地分支上合并远程分支

-  git pull origin next：master :取回origin主机的next分支，与本地的master分支合并
  等于
  > git fetch origin
  > git merge origin/next

---

## 8.15[周一]

- bug原因: 背景图url=null

---

## 8.16[周二]

-  Suggestions suggestions = getSuggestionsProvider()
                .getSuggestions(query, corporaToQuery);

- 实现关系
  
  Consumer--> SuggestionCursorReceiver

- SingleThreadNamedTaskExecutor：执行真正的查询任务

---

## 8.17[周三]

-  An absolute hierarchical URI reference follows the pattern:
 <scheme>://<authority><absolute path>?<query>#<fragment>
 
 Uri.Builder uriBuilder = new Uri.Builder()
                    .scheme(ContentResolver.SCHEME_CONTENT)
                    .authority(authority);

---

## 8.18[周四]

-    protected NamedTaskExecutor createSourceTaskExecutor() {
        ThreadFactory queryThreadFactory = getQueryThreadFactory();
        return new PerNameExecutor(SingleThreadNamedTaskExecutor.factory(queryThreadFactory));
    }

- SearchActivtiy:updateSuggestionsgs->getSuggestions()
  -> SuggestionsProviderImpl:getSuggestions()
  -> QueryTask:startQueries->startQuery()
  -> BatchingNamedTaskExecutor.execute()   //加入队列
  -> executeNextBatch()
  -> dispatch
  -> PerNameExecutor.execute()

  ---

  ## 8.19[周五]

  - public void clearSearchTextEditorFoucs() {
        try {
            Field fild_mSearchBar = android.support.v17.leanback.app.SearchFragment.class
                    .getDeclaredField("mSearchBar");
            fild_mSearchBar.setAccessible(true);
            Object mSearchBar = fild_mSearchBar.get(mFragment);
            Field fild_mSearchTextEditor = mSearchBar
                    .getClass().getDeclaredField("mSearchTextEditor");
            fild_mSearchTextEditor.setAccessible(true);
            EditText mSearchTextEditor = (EditText) fild_mSearchTextEditor
                    .get(mSearchBar);
            if (mSearchTextEditor.hasFocus())
                mSearchTextEditor.clearFocus();
        } catch (Exception e) {
            Log.e(TAG, "clearSearchTextEditorFoucs error ", e);
        }
    }

    public static boolean isSearchTextEditorHasFocus() {
        boolean ret = false;
        try {
            Field fild_mSearchBar = android.support.v17.leanback.app.SearchFragment.class
                    .getDeclaredField("mSearchBar");
            fild_mSearchBar.setAccessible(true);
            Object mSearchBar = fild_mSearchBar.get(mFragment);
            Field fild_mSearchTextEditor = mSearchBar
                    .getClass().getDeclaredField("mSearchTextEditor");
            fild_mSearchTextEditor.setAccessible(true);
            EditText mSearchTextEditor = (EditText) fild_mSearchTextEditor
                    .get(mSearchBar);
            ret = mSearchTextEditor.hasFocus();
        } catch (Exception e) {
            Log.e(TAG, "isSearchTextEditorHasFocus error ", e);
        }
        return ret;
    }

---

## 8.22[周一]

- git rebase origin/imaster
- rm 000
- rm 000*
- git format-patch -3
- git reset HEAD

- case VoiceRecognitionClient.ERROR_RECORDER:
                    errorMsg = "audio device unavailable, code:" + errorCode;
                    errorType = SpeechRecognizer.ERROR_AUDIO;
                    break;

- VoiceRecognizer: ErrorType=196608 ErrorCode=196609
  iQiyiSearchFragment: error msg: audio device unavailable, code:196609
  VoiceRecognizer: baidu voice recognize sdk error
  SearchBar: recognizer client error

---

## 8.23[周二]

- AudioRecord: restoreRecord_l() failed status -22
  AudioRecord-JNI: Error -22 during AudioRecord native read

- view.setDrawingCacheEnabled(true);  
  view.buildDrawingCache();  //启用DrawingCache并创建位图  
  Bitmap bitmap = Bitmap.createBitmap(view.getDrawingCache()); //创建一个DrawingCache的拷贝，因为DrawingCache得到的位图在禁用后会被回收  
  view.setDrawingCacheEnabled(false);  //禁用DrawingCahce否则会影响性能

---

## 8.24[周三]

- 1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次

  2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次

  3、设置Activity的android:configChanges="orientation|keyboardHidden"时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法  

---

## 8.25[周四]

- private void sendToForeground() {
    Notification note = new Notification(0, null,
            System.currentTimeMillis());
    note.flags |= Notification.FLAG_NO_CLEAR;
    startForeground(0, note);
  }
  在Service的onCreate()中调用，使Service处于foreground避免被系统杀死

---

## 8.26[周五]

- UncaughtExceptionHandler
- 写文件步骤:
   File dir = new File(Environment.getExternalStorageDirectory()
                        .getAbsolutePath() + File.separator + crash);
  if (!dir.exists())
      dir.mkdir();
  File file = new File(dir, fileName);
  FileOutputStream fos = new FileOutputStream(file);
  fos.write(crashReport.toString().getBytes());
  fos.close();

---

## 8.29[周一]

- http://blog.csdn.net/codenoodles
- 获取线程信息: android.os.Process.myPid()
- //获取logcat最后logSize行
    proc = (new ProcessBuilder())
          .command("/system/bin/logcat", "-v", "threadtime", "-t", String.valueOf(logSize), "-d","*:D")
          .redirectErrorStream(true)
          .start();
    fos.write("\n>>> Logcat <<<\n".getBytes());
    writeLog(fos, proc.getInputStream());

- /data/anr/traces.txt

---

## 8.30[周二]

- Watchdog: 当系统死掉后，使其回到能够工作的状态
  
  Android中Watchdog作用，当SystemServer(其中运行的服务: ActivityManager/WindowManager/PowerManager)出现死锁时，退出SystemServer,让init进程重启它，使其回到重新工作的状态。

- ActivityThread代表一个应用进程的主线程，职责是调度和执行运行其中的四大组件。

---

## 8.31[周三]

- 正则表达式匹配中文: [\u4e00-\u9fa5]
-  String reg = "[\u4e00-\u9fa5]";
   Pattern pattern = Pattern.compile(reg);
   Matcher matcher = pattern.matcher(query);
   matcher.find();
   StringBuilder builder = new StringBuilder();
   for(int i =0; i < matcher.groupCount(); i++){
       builder.append(matcher.group(i));
   }
   query = builder.toString();

- private boolean isZh() {
      Locale locale = getResources().getConfiguration().locale;
      String language = locale.getLanguage();
      if (language.endsWith("zh"))
          return true;
      else
        return false;
    }
  其中languag为语言码：
  zh：汉语
  en：英语

---
## 9.1 [周四]

-  HTTP: 应用层。包装数据
   
   TCP：传输层。 可靠 面向连接

   UDP:不可靠 面向数据报

   IP:网络层

   TCP/IP: 传输层

   Socket: 对TCP/IP的封装 接口

-   /**
     * 得到进程名字
     */
    public static String getCurrentProcessName(Context context) {

        int pid = android.os.Process.myPid();
        ActivityManager manager =
                (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
        for (ActivityManager.RunningAppProcessInfo process : manager.getRunningAppProcesses()) {

            if (process.pid == pid) {

                return process.processName;
            }
        }

        return null;
    }

---

## 9.2[周五]

- Crashed process and PIDs
  Terminated signal and fault address 
  Call stack

- 1.ANR（可见ANR）：
    发生场景：应用发生ANR。
    崩溃症状：系统弹出窗口询问用户选择“Force Close”或者“Wait”。
             “Force Close”将杀掉发生ANR的应用进程。“Wait”将会等待系统择机恢复此应用进程。
    发生原因：（1）应用主线程卡住，对其他请求响应超时。（2）死锁。（3）系统反应迟钝。（4）CPU负载过重。
  
  2.Force Close：
    发生场景：应用进程崩溃。
    崩溃症状：系统弹出窗口提示用户某进程崩溃。
    发生原因：空指向异常或者未捕捉的异常。
  
  3.Tombstones：
    发生场景：Native层崩溃
    崩溃症状：如果发生崩溃的native层和UI有关联（比如Browser），我们可以在UI上发现这个崩溃。
        如果发生崩溃的native层是在后台并且和UI没有直接联系，那么对于用户来说是不可见的，如果是debug版本可能会有Log打印出当时的底层现场。
    发生原因：各种各样，需要具体情况具体分析。
    
  4.系统服务崩溃（System Server Crash）：
    发生场景：系统服务是Android核心进程，此服务进程发生崩溃。
    崩溃症状：手机重启到Android启动界面
    发生原因：（1）系统服务看门狗发现异常。（2）系统服务发生未捕获异常。（3）OOM。（4）系统服务Native发生Tombstone。
  
  5.Kernel Panics：
    发生场景：Linux内核发生严重错误
    崩溃症状：手机从bootloader开始完全重启
    发生原因：（1）Linux内核内存空间发生内存崩溃。（2）内核看门狗发现异常。（3）空指针操作内核。

---

## 9.5[周一]

- ANR ： Waiting because no window has focus but there is a focused application that may eventually add a window when it finishes starting up.
> 这种问题主要是发生在两个应用页面之间切换的时候,这个临界点的时候,一个页面正在起来,另外一个页面已经"压栈",
  即失去焦点,并且在这个页面切换的时候快速点击返回back键,
  按照目前android系统的约定是先判断是否有window获得focus,
  发送按键message必须要有有效的focus窗口来接收,
  否则input event消息将会在系统里面Block,一旦Block,系统就开始计时(即timeout),
  时间一到即调用notifyANR开始处理timeout事件,表面现象APP无响应,然后崩溃掉.

- "鲁棒性": 健壮性（系统健壮性）

- SparseArray 和 ArrayMap 是 Android 专门针对内存优化而设计的取代 Java API 中的 HashMap 的数据结构。
  对于key是 int 类型则使用 SparseArray，可避免自动装箱过程；
  对于key为其他类型则使用 ArrayMap。HashMap 的查找和插入时间复杂度为O(1)的代价是牺牲大量的内存来实现的，
  而 SparseArray 和 ArrayMap 性能略逊于 HashMap，但更节省内存。

---

## 9.7[周三]

- 应用层 Crash 后台处理过程(笔记)

---

## 9.8[周四]

- 

---

## 9.9[周五]

- 
---

## 9.12[周一]

- 百度语音识别一次最长支持60s语音


---

## 9.13[周二]

- final Field fild1 = fild;
            Method methodStopReg = fild.get(mFragment).getClass().getMethod("stopRecognition");
            final Method methodStopReg2 = methodStopReg;
            AudioManager.OnAudioFocusChangeListener replaceAudioFocusChangeListener =
                    new AudioManager.OnAudioFocusChangeListener() {
                        @Override
                        public void onAudioFocusChange(int focusChange) {
                            Log.e(TAG, "focus is " + focusChange);
                            if(focusChange == AudioManager.AUDIOFOCUS_LOSS ){
                                try{
                                    methodStopReg2.invoke(fild1);
                                }catch(Exception e){
                                    e.printStackTrace();
                               }
                            }
                            //do nothing to override Android-M SearchBar listener due to SearchBar/Baidu SDK double request AudioFocus
                        }
                    };
---

## 9.14[周三]

- 
---

## 9.18[周日]

- Android 启动第三方程序

 //组件名称，第一个参数是包名，也是主配置文件Manifest里设置好的包名   第二个是类名，要带上包名 
    ComponentName com = new ComponentName("com.huawei", "com.huawei.Test1");
    Intent  intent = new Intent();
    //设置部件   
    intent.setComponent(com);   
    Test.this.startActivity(intent);

---

## 9.19[周一]

- null
---

## 9.20[周二]

- null
--

## 9.26[周一]
- null

---

## 9.27[周二]

- @TargetApi(11)
  public void test(){
    if(Build.VERSION.SDK_INT >= 11){
      //使用api11新加的方法
    }else{
      //低版本的处理方法
    }
  }
---

## 10.16[周日]
- RecyclerView设置GridView和ListView混排
> 思路： 使用GridLayoutManager，根据getItemViewType返回的type不同，如果是ListView就将其宽度铺满
代码:
GridLayoutManager gridlayoutManager = new Gridlayoutmananger(this,2);
gridlayoutManager.setSpanSizeLookUp(new GridLayoutManager.SpanSizeLookUp(){
     @Override
     publice int getSpanSize(int position){
          int type = mRecyclerVIew.getAdapter().getItemViewType();
          //根据type返回值
          return 1;//
     }
})

---

## 10.20[周四]
- 本地gradle脚本编译运行打包Android程序 :
  1.检查项目中的gradle版本/buildTools版本/compile sdk版本
  2.利用项目项目中的gradle wrapper进行编译打包
  	> ./gradlew clean 
  	> ./gradle assembleDebug
  或者 【编译打包、安装可以合并】
  	> ./gradlew clean
  	> ./gradlew installDebug