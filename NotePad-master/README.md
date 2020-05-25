# 安卓期中测试————NotePad部分功能实现

* 姓名: 龚崇敏</br>

* 学号: 116052017129</br>

* 班级: 数学与信息学院 软件工程3班(大数据方向)</br>

## 实验环境

* Windows10+Android Studio3.5.2</br>

* AS采用安卓10</br>

## 实验设置

原来源码是基于安卓6.0的，SDK版本为23，我比较喜欢安卓10的界面，于是把build.gradle(Module:app)</br>

设置成如下截图形式，即可运行在安卓10版本的手机模拟器下(方框中的是本次实验我添加的依赖包)</br>

<img src="https://github.com/TheFirstDreamzZZ/MidExam/blob/master/NotePad-master/ScreenShoot/setting.png" width="500" />

## 任务1

###     实验要求：NoteList中显示条目增加时间戳显示

<img src="https://github.com/TheFirstDreamzZZ/MidExam/blob/master/NotePad-master/ScreenShoot/task1.png" width="500" />

(1) 在notelist_item.xml布局文件中添加一个TextView，用来显示时间戳</br>

代码如下</br>

```JAVA
    <TextView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@android:id/text2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:textSize="12dp"
        android:gravity="center_vertical"
        android:paddingLeft="5dp"
        android:singleLine="true"
        android:text="132123"
        android:layout_margin="0dp"
        />
```
(2) 对于时间字段，我们可以在存入之前就格式化，也可以提取出来之后再格式化显示，这里我采用的是前一方式。</br>

在NoteEdit.java中找到updateNote函数，选取修改时间这一字段，将原来的插入语句，替换成如下语句</br>

```JAVA
        Date nowTime = new Date(System.currentTimeMillis());
        SimpleDateFormat sdFormatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String retStrFormatNowDate = sdFormatter.format(nowTime);
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, retStrFormatNowDate);
```

(3) 在NoteList.java中找到PROJECTION数组、dataColumns数组，添加 NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE 字段</br>

找到viewIDs数组，添加第一步的TextView的id.

```JAVA
    /**
     * The columns needed by the cursor adapter
     */
    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE //2
    };
```
###     实验结果截图：

<img src="https://github.com/TheFirstDreamzZZ/MidExam/blob/master/NotePad-master/ScreenShoot/timeset.png" width="375" />

## 任务2

###     实验要求：添加笔记查询功能（根据标题查询）

<img src="https://github.com/TheFirstDreamzZZ/MidExam/blob/master/NotePad-master/ScreenShoot/task2.png" width="500" />

* 这题我是通过SearchView控件实现的。(看看后面拓展能不能实现菜单栏的搜索框)

(1) 新建一个listview.xml布局，用来实现搜索布局(名字可自定义)</br>

代码如下</br>

```JAVA
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    >
    <SearchView
        android:id="@+id/sv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:iconifiedByDefault="false"
        android:queryHint="请输入搜索内容"
        >
    </SearchView>

    <ListView
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:drawSelectorOnTop="false" />
    <TextView
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        android:id="@android:id/empty"
        android:gravity="center"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:text="无搜索结果" />
</LinearLayout>
```
(2) 在NoteList.java创建专门的函数配置SearchView,在OnCreate函数,调用SearchView函数</br>

具体实现是通过创建一个cursor在数据库中模糊搜索,装配数据,在ListView中即时显示。</br>

SearchView函数代码如下:</br>

```JAVA
    private void SearchView(final SimpleCursorAdapter adapter) {
        SearchView searchView = findViewById(R.id.sv);

        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String query) {
                return false;
            }

            @Override
            public boolean onQueryTextChange(String s) {
                Cursor newCursor;

                if (!s.equals("")) {
                    String selection = NotePad.Notes.COLUMN_NAME_TITLE + " GLOB '*" + s + "*'";
                    newCursor = getContentResolver().query(
                            getIntent().getData(),
                            PROJECTION,
                            selection,
                            null,
                            NotePad.Notes.DEFAULT_SORT_ORDER
                    );
            newCursor = getContentResolver().query(
                            getIntent().getData(),
                            PROJECTION,
                            null,
                            null,
                            NotePad.Notes.DEFAULT_SORT_ORDER
                    );
                }
                adapter.swapCursor(newCursor); // 视图同步更新！
                return true;
            }
        });
    }
```

###     实验结果截图：

<img src="https://github.com/TheFirstDreamzZZ/MidExam/blob/master/NotePad-master/ScreenShoot/Search.png" width="375" />
