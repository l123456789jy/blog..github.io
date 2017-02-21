---
layout:     post
title:      "Gson的TypeToken使用"
subtitle:   ""
date:       2016-07-11 14:07:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Gson
---



```java

public class MainActivity extends AppCompatActivity {
    String jsonString = "[{\"id\":18,\"city\":\"test\",\"street\":\"test 1\",\"zipcode\":121209,\"state\":\"IL\",\"lat\":32.158138,\"lng\":34.807838},{\"id\":19,\"city\":\"test\",\"street\":\"1\",\"zipcode\":76812,\"state\":\"IL\",\"lat\":32.161041,\"lng\":34.810410}]";



    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        List<MapData> mUser = fromToJson(jsonString);
        Toast.makeText(this,mUser.toString(),Toast.LENGTH_SHORT).show();
    }

	//根据泛型返回解析制定的类型
    public  <T> T fromToJson(String json){
        Type type = new TypeToken<T>(){}.getType();
        Gson gson = new Gson();
        return gson.fromJson(json, type);
    }
}


```




##还有一种情况我们在安卓中经常的去解析一个类。通过泛型，那么我么你如何直接得到这个泛型呢。



```java
public abstract class OkBaseJsonParser<T> extends OkBaseParser<T> {
   //这里我们返回得到一个type
    public Type mType;

    public OkBaseJsonParser() {
        mType = getSuperclassTypeParameter(getClass());
    }

    protected abstract T parse(Response response) throws IOException;

    private static Type getSuperclassTypeParameter(Class<?> subclass) {
        //得到带有泛型的类
        Type superclass = subclass.getGenericSuperclass();
        if (superclass instanceof Class) {
            throw new RuntimeException("Missing type parameter.");
        }
        //取出当前类的泛型
        ParameterizedType parameter = (ParameterizedType) superclass;
        return $Gson$Types.canonicalize(parameter.getActualTypeArguments()[0]);
    }
}

```



##为什么我们需要type？




```java

        //Gso接受一个Type，返回一个我们解析的实例就可以了。
        return mGson.fromJson(body, mType);

```
