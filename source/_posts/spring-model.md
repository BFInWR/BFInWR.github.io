---
title: Spring中Model,ModelMap以及ModelAndView
date: 2018-11-19 20:25:00
categories: "Spring"
tags:
	- Spring
	- Model

---
## Spring中Model,ModelMap以及ModelAndView之间的区别

**model 都是为了从控制层直接返回前端所需的数据**

- ① Model
Model是一个接口，它的实现类为ExtendedModelMap，继承ModelMap类
```java
public class ExtendedModelMap extends ModelMap implements Model
```
<!-- more -->
- ② ModelMap
ModelMap继承LinkedHashMap，spring框架自动创建实例并作为controller的入参
```java
public class ModelMap extends LinkedHashMap<String,Object>
```

- ③ ModelAndView
顾名思义，ModelAndView指模型和视图的集合，既包含模型 又包含视图；ModelAndView的实例是开发者自己手动创建的，这也是和ModelMap主要不同点之一
列子
```java
@RequestMapping(value = "/mergeModel")
public ModelAndView mergeModel(Model model) {
    model.addAttribute("a", "a");//1添加模型数据
    ModelAndView mv = new ModelAndView("success");
    mv.addObject("a", "update");//2在视图渲染之前更新③处同名模型数据
    model.addAttribute("a", "new");//3修改1处同名模型数据
    //视图页面的a将显示为"update" 而不是"new"
    return mv;
}
```