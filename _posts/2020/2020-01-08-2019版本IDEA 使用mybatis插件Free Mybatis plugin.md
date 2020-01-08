---
layout:     post
title:      "2019版本IDEA 使用mybatis插件Free Mybatis plugin"
subtitle:   ""
date:       2020-01-08 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - idea
---





- ![微信截图_20200108160829.png](http://ww1.sinaimg.cn/large/9f723435gy1gap7xpl0tnj20w30nw760.jpg)

- 首先安装插件如图所示选择第一个插件安装，安装完毕重启IDEA


- 然后我们选择我们已经配置好的数据库如下图

![微信截图_20200108161048.png](http://ww1.sinaimg.cn/large/9f723435gy1gap7zdexzcj20cm0kwjrv.jpg)


- 在需要生成配置文件的数据库上右键，就会出现mybatis-generator选项打开这个



![微信截图_20200108161210.png](http://ww1.sinaimg.cn/large/9f723435gy1gap80tsudtj20wr0ivgmc.jpg)



- 就打开如上图所示配置窗口，这里我们使用默认的就行，如果不想使用lombok去掉这个勾选即可，然后我们选择ok即可



![微信截图_20200108161516.png](http://ww1.sinaimg.cn/large/9f723435gy1gap840otynj207r07j0sq.jpg)



- 就会生成以上文件,一个数据库的映射类，一个操作数据库的映射接口，和具体操作数据库的xml，主配置文件就在Configure里面这个自己生成


![微信截图_20200108161657.png](http://ww1.sinaimg.cn/large/9f723435gy1gap868pcxcj20i50b3t8v.jpg)



- 我们打开生成的接口具体操作数据的，就可以看到右边右箭头可以点击，就可以跳转到具体的映射xml

![微信截图_20200108161854.png](http://ww1.sinaimg.cn/large/9f723435gy1gap87thb5cj20ng0bugm8.jpg)



- 可以看到xml也可以直接跳转到具体的接口,下面就可以操作数据库了



   ```java
     private static SqlSessionFactory sqlSessionFactory;
        private static Reader reader;

        static {
            try {
                reader = Resources.getResourceAsReader("Configure.xml");
                sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        public static SqlSessionFactory getSession() {
            return sqlSessionFactory;
        }


        public static void main(String[] args) {
            SqlSession session = sqlSessionFactory.openSession();
            try {
                UserDao mapper = session.getMapper(UserDao.class);
                User user =mapper.selectByPrimaryKey(1L);
                if(user!=null){
                    String userInfo =user.toString();
                    System.out.println(userInfo);
                }
            } finally {
                session.close();
            }
        }
```





