---
layout: post
title:  "Dynamic Proxy"
date:   2021-04-12
excerpt: "Dynamic Proxy"
comments: true
tag:
- java
---

# 다이나믹프록시

---

## 스프링 데이터 JPA는 어떻게 동작하나

인터페이스 인스턴스가 어떻게 만들어 졌는가?

내가 구현하지 않았는데 누가 객체를 만들어줬을까?

- Spring AOP를 기반으로 동작하며 RepositoryFactorysupport에서 프록시 객체를 생성한다.

## 프록시 패턴

![dynamicproxy](https://myeongkwonhwang.github.io/assets/img/dynamicproxy/dynamicproxy.png)

- 프록시와 리얼 서브젝트가 공유하는 인터페이스가 있고, 클라이언트는 해당 인터페이스 타입으로 프록시를 사용한다.
- 클라이언트 프록시를 거쳐 리얼 서브젝트를 사용하기 때문에 프록시는 리얼 서브젝트에 대한 접근을 관리하거나 부가기능을 제공, 리턴값을 변경할 수도 있다.
- 리얼 서브젝트는 자신이 해야 할 일만 하면서 프록시를 사용하여(Single Responseibility Principle) 부가적인 기능 (접근 제한, 로깅, 트랜잭션 등)을 제공할 때 이런 패턴을 주로 사용한다.

## 다이나믹 프록시

런타임에 특정 인터페이스들을 구현하는 클래스 또는 인스턴스를 만드는 기술

프록시 인스턴스 만들기

- Object Proxy.newProxyInstance(ClassLoader, Interfaces, InvocationHandler)

{% highlight java %}
BookService bookService = (BookService) Proxy.newProxyInstance(BookService.class.getClassLoader()
		, newClass[]{BookService.class} , new InvocationHandler() {
					BookService bookService = new DefaultBookService();
					@Override
					public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
							if (method.getName().equals("rent")) {
								System.out.println("aaaa");
								Object invoke = method.invoke(bookService, args);
								System.out.println("bbbb");
								return invoke;
							}
							return method.invoke(bookService, args);
					}
			});
{% endhighlight %}

- 유연한 구조가 아니다.
- 스프링 AOP에 대한 더 자세한 내용은 토비의 스프링 3.1, 6장 AOP참고

## 클래스에 프록시가 필요하다면?(인터페이스가 없는 경우)

서브 클래스를 만들 수 있는 라이브러리를 사용하여 프록시를 만들 수 있다.

CGlib

- [https://github.com/cglib/cglib/wiki.com](https://github.com/cglib/cglib/wiki.com)
- 스프링, 하이버네이트가 사용하는 라이브러리
- 버전 호환성이 좋지 않아 서로다른 라이브러리 내부에 내장된 형태로 제공되기도 한다.

{% highlight java %}
MethodInterceptor handler = new MethodInterceptor() {
		BookService bookService = new BookService();
		@Override
		public Object intercept(Object o, Method method, Object[] objects, MethodProxymethodProxy) throws Throwable {
				return method.invoke(bookService, objects);
		}
};

BookService bookService = (BookService) Enhancer.create(BookService.class, handler);
{% endhighlight%}

Bytebuddy

- [https://bytebuddy.net/#/](https://bytebuddy.net/#/)
- 바이트 코드 조작 뿐 아니라 런타임(다이나믹) 프록시를 만들 때도 사용할 수 있다.

{% highlight java %}
Class<? extends BookService> proxyClass = new ByteBuddy().subclass(BookService.class)
                .method(named("rent")).intercpt(InvocationHandlerAdapter.of(new InvocationHandler() {
                        BookService bookService = new BookService();
                        @Override
                        public Object invoke(Object proxy, Method method, object[] args) throws Throwable {
                                System.out.println("aaa");
                                Object invoke = method.invoke(bookService, args);
                                System.out.println("bbb");
                                return invoke;
                        )
                }))
                .make().load(BookService.class.getClassLoader()).getLoaded();
BookService bookService = proxyClass.getConstructor(null).newInstance();
{% endhighlight %}

서브 클래스를 만드는 방법의 단점

- 상속을 사용하지 못하는 경우 프록시를 만들 수 없다.
    - Private 생성자만 있는 경우
    - Final 클래스인 경우
- 인터페이스가 있을 때는 인터페이스의 프록시를 만들어 사용할 것.

## 정리

런타임에 인터페이스 또는 클래스의 프록시 인스턴스 또는 클래스를 만들어 사용하는 프로그래밍 기법

다이나믹프록시 사용처

- 스프링 데이터 JPA
- 스프링 AOP
- Mockito
- 하이버네이트 Lazy initialzation

토비의 스프링 6장 꼭 읽어보도록 하자!!!
