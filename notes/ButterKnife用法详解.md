# ButterKnife用法详解
### 如何成为T型人才，垂直在一个行业中，必须要有一整套[知识体系](https://github.com/WeiSmart/Android-Advanced-CloudMap)，在这里，就一个字坚持~

## 前言

ButterKnife(黄油刀)是一种IOC框架，专注于Android中View控件，事件监听注入。而且，在性能方面的考虑，使用注解+APT运行时进行解析，放弃已往通过放射实现方式。在使用ButterKnife进行项目开发，用起来是比较简洁的，但是简洁的背后不代表容易，后期我会对[ButterKnife](https://jakewharton.github.io/butterknife/) 源码进行分析。谢谢大家~~

![butterKnife](https://github.com/WeiSmart/Android-Advanced-CloudMap/blob/master/screenshots/butterKnife.png)

## Contests

### 一、如何引入环境
```
android {
  ...
  // Butterknife requires Java 8.
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
```

- #### 依赖增加（java）

  ```
    implementation 'com.jakewharton:butterknife:10.2.1'
    annotationProcessor 'com.jakewharton:butterknife-compiler:10.2.1'
  ```

- #### 依赖增加（Kotlin）

  ```
    implementation 'com.jakewharton:butterknife:10.2.1'
    kapt 'com.jakewharton:butterknife-compiler:10.2.1'
  ```

  注：使用kapt需要在build.gradle文件引用（apply plugin: 'kotlin-kapt'）

### 二、不同场景布局绑定方式（ButterKnife.bind）

#### 2.1、**Activity**里在onCreate方法中加入ButterKnife.bind(this)

  ```
  public class MainActivity extends AppCompatActivity{  
      @Override  
      protected void onCreate(Bundle savedInstanceState) {  
          super.onCreate(savedInstanceState);  
          setContentView(R.layout.activity_main);  
          //绑定初始化ButterKnife  
          ButterKnife.bind(this);  
      }  
  }
  ```

#### 2.2、**Fragment**中先使用inflater返回一个view，再使用ButterKnife.bind(this, view)绑定

  ```
  public class ButterknifeFragment extends Fragment{  
      private Unbinder mUnbinder;  
      @Override  
      public View onCreateView(LayoutInflater inflater, ViewGroup container,  
                               Bundle savedInstanceState) {  
          View view = inflater.inflate(R.layout.fragment, container, false);  
          //返回一个Unbinder值（进行解绑），注意这里的this不能使用getActivity()  
          mUnbinder = ButterKnife.bind(this, view);  
          return view;  
      }  
  
      /** 
       * onDestroyView中进行解绑操作 
       */  
      @Override  
      public void onDestroyView() {  
          super.onDestroyView();  
          if(mUnbinder!=null){
          mUnbinder.unbind();
          }
      }  
  } 
  ```

  注：你需要对其进行解绑

#### 2.3、**Adapter**的内部类ViewHolder,只需要在构造函数调用ButterKnife.bind(this, view)进行绑定

  ```
  public class ButterknifeAdapter extends BaseAdapter {  
  
    @Override   
    public View getView(int position, View view, ViewGroup parent) {  
      ViewHolder holder;  
      if (view != null) {  
        holder = (ViewHolder) view.getTag();  
      } else {  
        view = inflater.inflate(R.layout.testlayout, parent, false);  
        holder = new ViewHolder(view);  
        view.setTag(holder);  
      }   
      holder.title.setText("xxx");
      holder.nmae.setText("xxx");
      return view;  
    }  
  
    static class ViewHolder {  
      @BindView(R2.id.title) TextView title;  
      @BindView(R2.id.name) TextView name;  
  
      public ViewHolder(View view) {  
        ButterKnife.bind(this, view);  
      }  
    }  
  }  
  ```

### 三、View绑定解锁(@BindView)

#### 3.1、单个控件绑定

  ```
   @BindView(R2.id.first_name)
   EditText firstName;
   @BindView(R2.id.middle_name)
   EditText middleName;
   @BindView(R2.id.last_name)
   EditText lastName;
  ```

#### 3.2、多控件一次性绑定

  ```
  public class MainActivity extends AppCompatActivity {  
  
      @BindViews({ R2.id.first_name, R2.id.middle_name,  R2.id.last_name})  
      List<EditText> nameViews ;  
  
      @Override  
      protected void onCreate(Bundle savedInstanceState) {  
          super.onCreate(savedInstanceState);  
          setContentView(R.layout.activity_main);  
          
          ButterKnife.bind(this);  
  
          nameViews.get( 0 ).setText( "hello 1 ");  
          nameViews.get( 1 ).setText( "hello 2 ");  
          nameViews.get( 2 ).setText( "hello 3 ");  
      }  
  }  
  ```

  

### 四、OnClick事件绑定解锁

#### 4.1、OnClick和OnLongClick单个点击事件处理

```
public class MainActivity extends AppCompatActivity {  

    @OnClick(R2.id.button1)
    public void onViewClicked() {
          Toast.makeText(this, "这是绑定的点击事件", Toast.LENGTH_SHORT).show();  
    }

    @OnLongClick(R2.id.button2)   
    public boolean onViewClickeds(){  
        Toast.makeText(this, "这是绑定的长按事件", Toast.LENGTH_SHORT).show();  
        return true ;  
    }  
} 
```

#### 4.2、多个点击事件处理

```
@OnClick({R2.id.button1, R2.id.button2})
    public void onViewClicked(View view) {
        switch (view.getId()) {
            case R.id.button1:
              Toast.makeText(this, "button1点击事件", Toast.LENGTH_SHORT).show();  
                break;
            case R.id.button2:
              Toast.makeText(this, "button2点击事件", Toast.LENGTH_SHORT).show();  
                break;
        }
    }
```

#### 4.3、自定义类型事件处理

- ##### ViewPager中监听事件OnPageSelected()

  ```
  @OnPageChange(R2.id.viewpager)
  public void onPageSelected(int position) {
          switch (position) {
              case 0:
  				//滑动到第一页
                  break;
              case 1:
                  //滑动到第二页
                  break;
          }
      }
  ```

- ##### RadioGroup+RadioButton中监听事件OnCheckedChanged()

  ```
  @OnCheckedChanged({R2.id.click1,R2.id.click2})  
      public void OnCheckedChangeListener(CompoundButton view, boolean ischanged ){  
          switch (view.getId()) {  
              case R.id.click1:  
                  if (ischanged){
                  //注意：这里一定要有这个判断，只有对应该id的按钮被点击了，ischanged状态发生改变，才会执行下面的内容  
                 //这里写你的按钮变化状态的UI及相关逻辑  
                  }  
                  break;  
              case R.id.click2:  
                  if (ischanged) {  
                      //这里写你的按钮变化状态的UI及相关逻辑  
                  }  
                  break;  
         
              default:  
                  break;  
          }  
      } 
  ```

- #####  其他事件

  | 注解                  |                           **功能**                           |
  | --------------------- | :----------------------------------------------------------: |
  | @OnEditorAction       |                        软键盘的功能键                        |
  | @OnFocusChange        |                           焦点改变                           |
  | @OnItemClick item     | 被点击(注意这里有坑，如果item里面有Button等这些有点击的控件事件的，需要设置这些控件属性focusable为false) |
  | @OnItemLongClick item |               长按(返回真可以拦截onItemClick)                |
  | @OnItemSelected       |                        item被选择事件                        |
  | @OnTextChanged        |                  EditText里面的文本变化事件                  |
  | @OnPageSelected       |                    ViewPager滑动选择事件                     |
  | @OnCheckedChanged     |                      RadioGroup选择事件                      |
### 五、Resource绑定解锁

#### 5.1、Bitmap图片资源绑定

```
@BindBitmap( R2.mipmap.icon )
Bitmap bitmap;
```

#### 5.2、String字符串资源绑定

```
@BindString(R2.string.name)  
String name;  
```

#### 5.3、String字符串数组资源绑定

```
<resources>  
    <string-array name="city">  
        <item>广州市</item>  
        <item>深圳市</item>  
        <item>汕头市</item>  
    </string-array>  
</resources> 

@BindArray(R2.array.city)  //绑定string里面array数组  
String [] citys ; 
```

#### 5.4、ColorResId 资源绑定：

```
@BindColor(R2.color.white) 
int white;
```

#### 5.5、Dimen资源绑定

```
@BindDimen(R2.dimen.width)
int width;
```

#### 5.6、Drawable资源绑定

```
@BindDrawable(R2.drawable.image) 
Drawable image;
```

### 六、基本类型绑定解锁

#### 6.1、绑定Int类型

```
@BindInt(R2.int.size)
int size;
```

#### 6.2、绑定Bool类型

```
@BindBool(R2.bool.flag)
boolean flag;
```

#### 6.3、绑定Float类型

```
@BindFloat(R2.dimen.size)
float size;
```

### 七、其他用法

#### 7.1、通过apply方法可以一次性对列表中的所有视图进行操作

  ```
  @BindViews({ R2.id.first_name, R2.id.middle_name, R2.id.last_name })
  List<EditText> nameViews;
  
  ButterKnife.apply(nameViews, DISABLE);
  ButterKnife.apply(nameViews, ENABLED, false);
  
  //Action和Setter接口允许指定简单行为。
  static final ButterKnife.Action<View> DISABLE = new ButterKnife.Action<View>() {
    @Override public void apply(View view, int index) {
      view.setEnabled(false);
    }
  };
  static final ButterKnife.Setter<View, Boolean> ENABLED = new ButterKnife.Setter<View, Boolean>() {
    @Override public void set(View view, Boolean value, int index) {
      view.setEnabled(value);
    }
  };
  
  //Android属性也可以与apply方法一起使用。
  ButterKnife.apply(nameViews, View.ALPHA, 0.0f);
  ```

  #### 7.2、View和事件绑定的时候，出现找不到目标视图，将会导致异常发生

若要抑制此行为并创建可选绑定，请在字段中添加@Nullable批注或在方法中添加@Optional批注。

```
@Nullable 
@BindView（R2.id.title）TextView mTitle;

@Optional 
@OnClick（R2.id.click）
public void onViewClicked() {
   // TODO ...
}
```



## 赞赏

如果Open Coder对您有很大帮助，您愿意扫描下面的二维码，只需要支付0.01，表达您对我认可和鼓励。
> 非常感谢您的捐赠。谢谢!

<div align="center">
<img src="https://github.com/WeiSmart/tablayout/blob/master/screenshots/weixin_pay.jpg" width=20%>
<img src="https://github.com/WeiSmart/tablayout/blob/master/screenshots/zifubao_pay.jpg" width=20%>
</div>
---

## About me
- #### Email:linwei9605@gmail.com   
- #### Blog: [https://offer.github.io/](https://offer.github.io/)
- #### 掘金: [https://juejin.im/user/59091b030ce46300618529e0](https://juejin.im/user/59091b030ce46300618529e0)

## License
```
   Copyright 2020 offer

      Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.```

```
