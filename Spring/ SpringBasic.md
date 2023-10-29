<details>
<summary>IoC 컨테이너</summary>
<div markdown="1">

1. IoC 컨테이너
    1. IoC (Inversion of Control): 제어 역전
    2. 일반적으로 프로그래밍을 작성할 때 프로그램이 흘러가는 흐름이나 생성되는 객체에 대한 제어권을 개발자가 가지는 것과 달리 프레임워크가 가지는 것을 의미한다.
    3. 개발자가 코드의 흐름이나 객체 생성에 관련된 코드를 프로그래밍 코드에 직접 작성하는 것이 아닌 프레임워크가 사용하는 파일에 작성하면 이를 토대로 프레임워크가 객체를 생성하여 반환하고 코드가 동작하는 순서를 결정하게 된다는 의미이다.
    4. IoC 컨테이너는 한 id에 대해 객체를 생성한 뒤 보관한다.

2. POJO Class
    1. POJO(Plain Old Java Object): 자바 모델이나 기능, 프레임워크 등에 따르지 않고 홀로 독립적이며 단순한 기능만을 가진 객체들을 의미한다.
    2. 자바에서는 이러한 객체들을 Bean이라고 부른다.

3. IOC 컨테이너 종류
    1. BeanFactory
        1. 클래스를 통해 객체를 생성하고 이를 전달한다.
        2. 상속 등 객체 간의 관계를 형성하고 관리한다.
        3. Bean에 관련된 설정을 위한 xml 파일은 즉시 로딩하지만 객체는 개발자가 요구할 때 생성한다.
        4. XmlBeanFactory(legacy)
            1. getBean 메소드 실행 시 객체가 생성된다.
    2. ApplicationContext
        1. 클래스를 통해 객체를 생성하고 이를 전달한다.
        2. 상속 등 객체 간의 관계를 형성하고 관리한다.
        3. 국제화 지원 등 문자열에 관련된 다양한 기능을 제공한다.
        4. 리스너로 등록되어 있는 Bean에 이벤트를 발생시킬 수 있다.
        5. Bean에 관련된 설정을 위한 xml 파일은 즉시 로딩하면서 객체를 미리 생성해서 가지고 있다.(따로 설정 가능)
        6. ClassPathXmlApplicationContext(패키지 내부)
        7. FileSystemXmlApplicationContext(패키지 외부)
        8. XmlWebApplicationContext

</div>
</detail>

<details>
<summary>Bean 객체 생성</summary>
<div markdown="1">

1. Bean 태그의 기본 속성
    1. class: 객체를 생성하기 위해 사용할 클래스를 지정한다.
    2. id: getBean 메서드를 통해 객체의 주소값을 받아올 수 있다.
    3. lazy-init: xml 로딩할 때 객체를 생성하지 않고 getBean 메소드를 호출할 때 객체가 생성된다.(default=false)
    ```
    <bean class="beans.TestBean" lazy-init="true"/>
    ```
    4. scope: 객체의 범위를 설정한다.
        1. singleton: 객체를 하나만 생성해서 사용한다.(default)
        2. prototype: 객체를 가져올 때마다 객체를 생성한다.
    ```
    <!-- scope=prototype - xml을 로딩할 때 객체가 생성되지 않는다. -->
    <!-- 객체를 가져올 때마다 객체를 생성한다. -->
    <bean class='beans.TestBean' scope='prototype'/>
    ```

</div>
</details>

<details>
<summary>Bean 객체의 생명주기</summary>
<div markdown="1">

1. Spring의 Bean은 다음과 같은 상황일 때 객체가 소멸된다.
    1. IoC 컨테이너가 종료될 때
2. 객체가 생성되면 가장 먼저 생성자가 호출된다.
    1. init-method: 생성자 호출 이후 자동으로 호출된다.
    2. destroy-method: 객체가 소멸될 때 자동으로 호출된다.
    ```
    <bean id="lazy" class="beans.TestBean" lazy-init="true" init-method="method_name" destroy-method="method_name"/>
    ```
    3. default-init-method: init-method를 설정하지 않은 경우 자동으로 호출된다.
    4. default-destroy-method: destroy-method를 설정하지 않은 경우 자동으로 호출된다.
    ```
    <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       default-init-method="method name"
       default-destroy-method="method name">
    ```
    5. 만약 default-init-method 설정 후 init-method도 설정한다면 init-method가 호출된다.(destroy도 동일)
    6. bean에 init-method를 정의하지 않고, 클래스 내 default-init-method가 존재하지 않더라도 객체는 그대로 생성되며 오류를 발생시키지 않는다.(그냥 무시)

</div>
</details>