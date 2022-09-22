# SpringMVC
## SpringMVC简介
SpringMVC是一款基于Servlet API的web框架，并基于前端控制器的模式被设计。前端控制器有一个中央的控制器（DispatcherServlet），通过一个共享的算法来集中分发请求，请求实际是通过可配置的委托组件（@Controller类）来处理的。  
> Spring Boot遵循不同的初始化流程。相较于hooking到servlet容器的生命周期中，Spring Boot使用Spring配置来启动其自身和内置servlet容器。filter和servlet声明在在spring配置中被找到并且被注入到容器中。

