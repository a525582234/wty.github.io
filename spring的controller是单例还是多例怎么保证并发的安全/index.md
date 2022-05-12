# Spring's Controllers are single or multiple instances and how to secure concurrency？


### ANSWER：

| The controller is singleton by default, so don't use non-static member variables, otherwise data logic will be confused. It is because of this single instance that it is not thread-safe. |
| ------------------------------------------------------------ |

### Validation：

```java
package com.riemann.springbootdemo.controller;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * @author riemann
 * @date 2019/07/29 22:56
 */
@Controller
public class ScopeTestController {

    private int num = 0;

    @RequestMapping("/testScope")
    public void testScope() {
        System.out.println(++num);
    }

    @RequestMapping("/testScope2")
    public void testScope2() {
        System.out.println(++num);
    }

}
```

We first visit http://localhost:8080/testScope and get an answer of 1.

We then visit http://localhost:8080/testScope2 and get an answer of 2.



**gets a different value, which is thread-unsafe.**



Next, we'll add more instances of the controller @Scope("prototype")

```java
package com.riemann.springbootdemo.controller;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * @author riemann
 * @date 2019/07/29 22:56
 */
@Controller
@Scope("prototype")
public class ScopeTestController {

    private int num = 0;

    @RequestMapping("/testScope")
    public void testScope() {
        System.out.println(++num);
    }

    @RequestMapping("/testScope2")
    public void testScope2() {
        System.out.println(++num);
    }

}
```

We still first visit http://localhost:8080/testScope and get the answer 1.

We then visit http://localhost:8080/testScope2 again and get the same answer of 1.



I'm sure it's easy to see that.

| Single instances are unsafe and can lead to reuse of attributes. |
| ------------------------------------------------------------ |

### **Solutions**:

- Do not define member variables in controllers.
- In case a non-static member variable has to be defined, it is set to multi-instance mode by annotating @Scope("prototype").
- Using ThreadLocal variables in the Controller.

### Additional notes:

The spring bean scope has the following 5 scopes:

**singleton**:Singleton mode, when spring creates the applicationContext container, spring will want to initialize all instances of the scope, plus lazy-init to avoid pre-processing.

**prototype**:Prototype mode, where each time the bean is fetched via getBean a new instance is created and spring will no longer manage it after it is created.

(The following is only used under web projects)

**request**:The web should understand the domain of request, that is, each request generates a new instance, and prototype is different is created, the next management, spring is still listening.

**session**:per session, as above.

**global session**:A global web domain, similar to an application in a servlet.



Author:*riemann_*

*blog.csdn.net/riemann_/article/details/97698560*

