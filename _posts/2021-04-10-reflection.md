---
layout: post
title:  "Reflection"
date:   2021-04-09
excerpt: "Reflection "
comments: true
tag:
- java
---

# 리플렉션

---

## 리플렉션API 1 : 클래스 정보 조회

리플렉션의 시작은 Class<T>

### Class<T>에 접근하는 방법

- 모든 클래스르 ㄹ로딩 한 다음 Class<T>의 인스턴스가 생긴다. "타입.class"로 접근할 수 있다.
- 모든 인스턴스는 getClass()메소드를 가지고 있다. "인스턴스.getClass()로 접근할 수 있다.
- 클래스를 문자열로 읽어오는방법

{% highlight java %}
// Type.class로 접근하는 방법
Class<Book> bookClass = Book.class;

// 인스턴스로 접근하는 방법
Book book = new Book();
Class<? extends Book> aClass = book.getClass();

// 클래스 문자열로 읽어오는 방법 클래스가 없다면 ClassNotFoundException발생
Class<?> aClass1 = Class.forName("me.mkhwang.Book");
{% endhighlight %}

### Class<T>를 통해 할 수 있는것

필드, 메소드, 상위클래스, 인터페이스, 애노테이션, 생성자 등...

{% highlight java %}
// Type.class로 접근하는 방법
Class<Book> bookClass = Book.class;
// 인스턴스로 접근하는 방법
Book book = new Book();

Arrays.stream(bookClass.getDeclaredFields()).forEach(f -> {
    try {
        f.setAccessible(true); //접근제어자 무시
        int modifiers = f.getModifiers();
        System.out.printf("%s %s %s %s \n", 
                f, f.get(book), Modifier.isPrivate(modifiers), Modifier.isStatic(modifiers));
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    }
});

//메서드
Arrays.stream(bookClass.getMethods()).forEach(System.out::println);

//생성자
Arrays.stream(bookClass.getDeclaredConstructors()).forEach(System.out::println);

//상속
System.out.println(bookClass.getSuperclass());

//인터페이스
Arrays.stream(bookClass.getInterfaces()).forEach(System.out::println);
{% endhighlight %}

## 애노테이션과 리플렉션

### 중요 애노테이션

- @Retention : 해당 애노테이션을 언제까지 유지할 것인가? 소스, 클래스, 런타임
- @Inherit: 해당 애노테이션을 하위 클래스까지 전달할 것인가?
- @Target: 어디에 사용할 수 있는가?

{% highlight java %}
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.FIELD})
@Inherited
public @interface MyAnnotation {
}
{% endhighlight %}

컴파일 후 바이트코드에서 확인하려면 javap -c -v 옵션 활용

### 리플렉션

- getAnnotations() : 상속받은 (@Inherit) 애노테이션까지 조회
- getDeclaredAnnotations() : 자기 자신에만 붙어있는 애노테이션 조회

## 리플렉션 API 2 : 클래스 정보 수정 또는 실행

- Class.newInstance() 는 deprecated 됐으며, 생성자를통해 만들어야 한다.
- 생성자로 인스턴스 만들기 - Cnstructor.newInstance(params)
- 필드값 접근하기/설정하기

  특정 인스턴스가 가지고 있는 값을 가져오는 것이기 때문에 인스턴스가 필요하다.

  Field.get(object), Field.set(object, value)

  Static 필드를 가져올 때는 objec가 없어도 되니 null을 넘기면 된다.

{% highlight java %}
Class<?> bookClass = Class.forName("me.mkhwang.Book");
Constructor<?> constructor = bookClass.getConstructor(String.class);
Book book = (Book) constructor.newInstance("myBook");
System.out.println(book);

//필드 값 변경
Field b = Book.class.getDeclaredField("B");
System.out.println(b.get(book));
b.set(book, "BBBBB");
System.out.println(b.get(book));

//invoke가 해당 메소드 실행
Method c = Book.class.getDeclaredMethod("c");
Object invoke = c.invoke(book);

//method에 parameter가 있는경우 type도 넣어준다 primitive / reference type체크 함.
Method d = Book.class.getDeclaredMethod("sum", int.class, int.class);
Object invoke1 = d.invoke(book, 1, 2);
System.out.println(invoke1);
{% endhighlight %}

## 나만의 DI 프레임워크 만들기

@Inject 라는 애노테이션 만들어서 필드 주입 해주는 컨테이너 만들기

{% highlight java %}
public static <T> T getObject(Class<T> classType){
    T instance = createInstance(classType);
    Arrays.stream(classType.getDeclaredFields()).forEach(f -> {

        if (f.getAnnotation(Inject.class) != null){
            Object fieldInstance = createInstance(f.getType());
            f.setAccessible(true);
            try {
                f.set(instance, fieldInstance);
            } catch (IllegalAccessException e) {
                throw new RuntimeException(e);
            }
        }

    });
    return instance;
}

private static <T> T createInstance(Class<T> classType) {
    try {
        return classType.getConstructor(null).newInstance();
    } catch (InstantiationException | NoSuchMethodException | InvocationTargetException | IllegalAccessException e) {
        throw new RuntimeException(e);
    }
}
{% endhighlight %}

지나친 사용은 성능 이슈를 야기할 수 있다. 반드시 필요한 경우에만 사용할 것.

컴파일 타임에 확인되지 않고 런타임 시에만 발생하는 문제를 만들 가능성이 있다.

접근 지시자를 무시할 수 있다.

사용 예)

스프링 : 의존성 주입, MVC뷰 에서 넘어온 데이터를 객체에 바인딩 할때,

하이버네이트 : @Entity 클래스에 Setter가 없다면 리플렉션을 사용한다.
