# verticle名称映射到一个verticle工厂的规则
当部署 verticle(s) 使用一个名称，该名称用于选择将实例化 verticle(s) 的实际verticle工厂。

verticle的名称可以有前缀-它是一个字符串, 后跟一个冒号，它如果存在，则会被用来查找工厂，如

```
js:foo.js // Use the JavaScript verticle factory
groovy:com.mycompany.SomeGroovyCompiledVerticle // Use the Groovy verticle factory
service:com.mycompany:myorderservice // Uses the service verticle factory
```

如果没有前缀，则 Vert.x 将寻找一个后缀和使用，例如查找工厂，

```
foo.js // Will also use the JavaScript verticle factory
SomeScript.groovy // Will use the Groovy verticle factory
```

如果没有前缀或后缀，则 Vert.x 会假设它是 Java 完整类名称 (FQCN)，然后实例化。
