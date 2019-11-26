# spring的IOC
1. 什么是IOC  
IOC最主要是完成了完成对象的创建和依赖的管理注入等等
2. 控制反转  
所谓控制反转，就是把原先我们代码里面需要实现的对象创建、依赖的代码，反转给容器来帮忙实现。那么必然的我们需要创建一个容器，同时需要一种描述来让容器知道需要创建的对象与对象的关系。这个描述最具体表现就是我们可配置的文件
## BeanFactory
  BeanFactory是IOC容器的核心接口，它的职责包括：实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。BeanFactory只是个接口，并不是IOC容器的具体实现，但是Spring容器给出了很多种实现，如 DefaultListableBeanFactory、XmlBeanFactory、ApplicationContext等，其中XmlBeanFactory就是常用的一个，该实现将以XML方式描述组成应用的对象及对象间的依赖关系
## FactoryBean说明
通常是用来创建比较复杂的bean，一般的bean 直接用xml配置即可，但如果一个bean的创建过程中涉及到很多其他的bean 和复杂的逻辑，用xml配置比较困难，这时可以考虑用FactoryBean 该类可以自定义bean加入到spring管理的bean中
```
public class GuavaSessionFactory {
    public void say() {
        System.out.println("hello world");
    }
}
@Component
public class CustomSpringBean implements FactoryBean<GuavaSessionFactory>, InitializingBean {

    private GuavaSessionFactory guavaSessionFactory;

    @Override
    public GuavaSessionFactory getObject() throws Exception {
        if (this.guavaSessionFactory == null) {
            afterPropertiesSet();
        }
        return this.guavaSessionFactory;
    }

    @Override
    public Class<?> getObjectType() {
        return this.guavaSessionFactory == null ? GuavaSessionFactory.class : this.guavaSessionFactory.getClass();
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        this.guavaSessionFactory = new GuavaSessionFactory();
    }
}

```
## ApplicationContext
