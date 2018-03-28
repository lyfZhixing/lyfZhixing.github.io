---
title: mybatis中的mapper
date: 2018-03-28 14:47:30
tags: [mybatis]  
categories: 
- 环境搭建
- 配置文件
---
mybatis与spring整合时常遇到的问题  
<!-- more -->   
 ##### mapper.xml中容易出错的方法和区别:  
1. selectByExample和selectByExampleWithBLOBs的区别(包含Example的使用)：  
 如果想要搜索结果包含大字段类型，则必须使用selectByExampleWithBLOBs。无需检索大字段，则使用selectByExample；  
2. insertSelective和insert的区别：  
当有部分字段未设值时，使用insertSelective：当有所有字段均已设值时，使用insert；   
3. 修改操作：  
updateByExample  
如果example定义了两个字段，数据库共4个字段，则修改数据库的两个字段，其余两个字段改为null；  
updateByExampleSelective  
如果example定义了两个字段，数据库共4个字段，则修改数据库的两个字段，其余两个字段不动；  
updateByExampleWithBLOBs和updateByExample相比此方法可以修改大字段类型，其余性质和updateByExample相同  
updateByPrimaryKey  
如果record定义了两个字段，其中有一个字段是主键，数据库共4个字段，则根据主键修改数据库的两个字段，其余两个字段改为null；  
updateByPrimaryKeySelective　  
如果record定义了两个字段，其中有一个字段是主键，数据库共4个字段，则根据主键修改数据库的两个字段，其余两个字段不动；  
updateByPrimaryKeyWithBLOBs和updateByPrimaryKey相比此方法可以修改大字段类型，其余性质和updateByPrimaryKey相同   

 ##### Mapper.xml语句中parameterType向SQL语句传参的两种方式：#{}和${}  
经常使用的是#{},因为这种方式可以防止SQL注入，#{}这种方式SQL语句是经过预编译的，把#{}中间的参数转义成字符串，举个例子：  


```
select * from student where student_name = #{name}
```

预编译后,会动态解析成一个参数标记符?：

```
select * from student where student_name = ?
```

而使用${}在动态解析时候，会传入参数字符串

```
select * from student where student_name = 'lyrics'
```


> 总结：  
#{} 这种取值是编译好SQL语句再取值  
${} 这种是取值以后再去编译SQL语句  
#{}方式能够很大程度防止sql注入。  
$方式无法防止Sql注入。  
$方式一般用于传入数据库对象，例如传入表名.  
一般能用#的就别用$.  

 ##### mybatis与spring整合中service层的空指针异常  
1. controller层实例化service会出现空指针，应该用@Autowired自动装配注入service；  
2. service层实现类中也要用@Autowired注入mapper；    

 ##### Failed to convert value of type 'java.lang.String' to required type 'java.util.Date';  
发生这一错误的主要原因是Controller类中需要接收的是Date类型，但是在页面端传过来的是String类型，最终导致了这个错误。
这里提供两种解决方案，一种是局部转换，一种是全局转换。  
- 局部转换  
```
@RequestMapping("/updateItems")
	public ModelAndView updateItems(Date createtime){
		
		ItemsCustom itemsCustom = new ItemsCustom();
		itemsCustom.setCreatetime(createtime);
		//....
		ModelAndView mv = new ModelAndView();
		//....
		return mv;
		
	}
	 @InitBinder  
	    public void initBinder(WebDataBinder binder, WebRequest request) {  
	          
	        //转换日期  
	        DateFormat dateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
	        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, true));// CustomDateEditor为自定义日期编辑器  
	    }  
```  
- 全局转换  
1. 创建CustomDate类实现WebBindingInitializer  
```
public class CustomDate implements WebBindingInitializer{  
  
    @Override  
    public void initBinder(WebDataBinder binder, WebRequest request) {  
        // TODO Auto-generated method stub  
        //转换日期  
        DateFormat dateFormat=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, true));  
    }  
}  
```  
2. 在Spring-MVC.xml中配置日期转换  
```
	<!-- 日期转换 -->  
    <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">  
        <property name="webBindingInitializer">  
            <bean class="lyf.CustomDate"/>  
        </property>  
    </bean>  
```
