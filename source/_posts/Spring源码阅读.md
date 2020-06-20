---
thumbnail: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=190035761,2302710927&fm=26&gp=0.jpg
title: Spring源码阅读
tags: 'Spring,Java'
categories: Spring
widgets:
  - type: recent_posts
    position: right
  - type: toc
    position: right
toc: true
date: 2020-06-11 14:15:05
---




### 下载和编译源码

0. 阅读README.md, 找到`Build from Source`阅读
1. fork`https://github.com/spring-projects/spring-framework`
2. clone`https://github.com/zxw7606/spring-framework` 
3. 切换到项目的tag`v5.2.7.RELEASE`分支 
3. 编译命令`gradlew build -x test` 

<!-- more -->

## 学习目标
1. 想知道Bean是在什么时候创建的,以及Bean中包含的属性
2. 想对Bean的生命周期中途进行改造
3. 想知道Spring的AOP是什么时候对Bean进行代理


## 初始化Bean的信息

### DefaultListableBeanFactory

Bean是怎么实现的,看一个测试用例获取Bean的流程,有一个DefaultListableBeanFactory

DefaultListableBeanFactory的的项目结构,可以看出 这个类主要的实现左半边的增删改查Bean,右半边的存储Bean

![DefaultListableBeanFactory的的项目结构](https://panjier0409.github.io/tuchuang/img20200612172157.png)



```java

@Test
void test() {
    DefaultListableBeanFactory xbf = new DefaultListableBeanFactory();
    new XmlBeanDefinitionReader(xbf).loadBeanDefinitions(RESOURCE_CONTEXT);
}

```

**主要的步骤在XmlBeanDefinitionReader#loadBeanDefinitions,默认实现为DefaultBeanDefinitionDocumentReader**

<pre>
  <code>
/**
 * 
 * 将xml中的每个bean实例化成BeanDefinition,并注册到注册中心中
 * @param doc the DOM document 解析xml文件的文档
 * @param resource xml文件的resource对象, 目的是为了存储xml的上下文
 */
public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
    BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();
    int countBefore = getRegistry().getBeanDefinitionCount();
    <b style="color: red">documentReader.registerBeanDefinitions(doc, createReaderContext(resource));</b>
    return getRegistry().getBeanDefinitionCount() - countBefore;
}
  </code>
</pre>

<pre>
 <code>
@Override
public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {
    this.readerContext = readerContext;
    <font color="red"><b>doRegisterBeanDefinitions(doc.getDocumentElement());</b></font>
}


/**
 * Register each bean definition within the given root {@code <beans/>} element.
 */
@SuppressWarnings("deprecation")  // for Environment.acceptsProfiles(String...)
protected void doRegisterBeanDefinitions(Element root) {
 
    // 默认用 BeanDefinitionParserDelegate解析器
    BeanDefinitionParserDelegate parent = this.delegate;
    this.delegate = createDelegate(getReaderContext(), root, parent);

    if (this.delegate.isDefaultNamespace(root)) {
        // 判断这个Beans的配置环境,可能为dev或者prod等
        String profileSpec = root.getAttribute(PROFILE_ATTRIBUTE);
        if (StringUtils.hasText(profileSpec)) {
            String[] specifiedProfiles = StringUtils.tokenizeToStringArray(
                    profileSpec, BeanDefinitionParserDelegate.MULTI_VALUE_ATTRIBUTE_DELIMITERS);
            // We cannot use Profiles.of(...) since profile expressions are not supported
            // in XML config. See SPR-12458 for details.
            if (!getReaderContext().getEnvironment().acceptsProfiles(specifiedProfiles)) {
                if (logger.isDebugEnabled()) {
                    logger.debug("Skipped XML bean definition file due to specified profiles [" + profileSpec +
                            "] not matching: " + getReaderContext().getResource());
                }
                return;
            }
        }
    }
    // 解析元素之前的自定义配置
    preProcessXml(root);
    <b style="color: red">parseBeanDefinitions(root, this.delegate);</b> 
    postProcessXml(root);
    // 回朔算法, 目的是为了代理能够解析父XML的代理同时解析子XML
    this.delegate = parent;
}


/**
 * Parse the elements at the root level in the document:
 * "import", "alias", "bean".
 * @param root the DOM root element of the document
 */
protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {
    if (delegate.isDefaultNamespace(root)) {
        NodeList nl = root.getChildNodes();
        for (int i = 0; i < nl.getLength(); i++) {
            Node node = nl.item(i);
            if (node instanceof Element) {
                Element ele = (Element) node;
                // 检查每个dom的标签的命名空间的来源,如果是spring自带的话那么用spring
                // 自己的解析器解析每个dom来生成BeanDefine
                if (delegate.isDefaultNamespace(ele)) {
                    <b style="color: red">parseDefaultElement(ele, delegate);</b> 
                }
                else {
                    // 这里我们看一下自定义的dom
                    <b style="color: red">delegate.parseCustomElement(ele);</b>
                }
            }
        }
    }
    else {
        delegate.parseCustomElement(root);
    }
}
 </code>
</pre>

**BeanDefinitionParserDelegate#parseCustomElement**
<pre>
 <code>
/**
 * Parse a custom element (outside of the default namespace).
 * @param ele the element to parse
 * @param containingBd the containing bean definition (if any)
 * @return the resulting bean definition
 */
@Nullable
public BeanDefinition parseCustomElement(Element ele, @Nullable BeanDefinition containingBd) {
    String namespaceUri = getNamespaceURI(ele);
    if (namespaceUri == null) {
        return null;
    }
    // 获取Map中对应key为namespaceUri的NamespaceHandler
    NamespaceHandler handler = this.readerContext.getNamespaceHandlerResolver().resolve(namespaceUri);
    if (handler == null) {
        error("Unable to locate Spring NamespaceHandler for XML schema namespace [" + namespaceUri + "]", ele);
        return null;
    }
    <b style="color: red">return handler.parse(ele, new ParserContext(this.readerContext, this, containingBd));</b> 
}
 </code>
</pre>

**解析后的BeanDefinition将存放到beanDefinitionMap中**



## `AbstractAutowireCapableBeanFactory`如何创建Bean的实例,


**方式1** 
如果Bean的beforeInstantiationResolved为True的话
1. `InstantiationAwareBeanPostProcessor#postProcessBeforeInstantiation`
2. `BeanPostProcessor#postProcessAfterInstantiation`

**方式2** 

1. `MergedBeanDefinitionPostProcessor#postProcessMergedBeanDefinition`
2. `InstantiationAwareBeanPostProcessor#postProcessAfterInstantiation`
3. `InstantiationAwareBeanPostProcessor#postProcessProperties`初始化属性
4. `BeanPostProcessor#postProcessBeforeInitialization`
5. `InitializingBean#afterPropertiesSet`
6. `BeanPostProcessor#postProcessAfterInitialization`
7. 如果是FactoryBean, 那就返回`object = factory.getObject();`

### 小节
这些PostProcessor一般是对Bean的增强,比如说将@Configuration注解的类描述成一个`BeanDefinition`,但其中的带有@Bean注解的方法
是通过PostProcessor解析并且注册到Spring中


例如SpringBoot中`spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories`

![org.springframework.boot.autoconfigure.EnableAutoConfiguration](https://panjier0409.github.io/tuchuang/img/img20200620125945.png)

注解类`org.springframework.boot.autoconfigure.EnableAutoConfiguration`

![ImportSelector](https://panjier0409.github.io/tuchuang/img/img20200620130112.png)

`ImportSelector`接口`String[] selectImports(AnnotationMetadata importingClassMetadata)`方法选择了需要导入的@Configuation类的类名

因此我们可以通过自定义注解`@EnableXXX`中标记`Import(Selector)`来实现自动配置