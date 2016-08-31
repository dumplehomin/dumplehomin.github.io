---
layout: post
title: "Prototype"
date: 2016-08-23
image: '/assets/img/dica-rapida-2/main.png'
description: 'JavaScript'
main-class: 'js'
color: '#EB7728'
tags:
- javaScript
- prototype
- 자바스크립트
- 프로토타입
categories: javascript
twitter_text: ''
introduction: "객체의 원형인 프로토타입을 이용하여 새로운 객체를 만들어내는 프로그래밍 기법이다. 이렇게 만들어진 객체 역시 자기자신의 프로토타입을 갖는다."
---

## 프로토타입 기반 프로그래밍이란?
> 객체의 원형인 프로토타입을 이용하여 새로운 객체를 만들어내는 프로그래밍 기법이다. 이렇게 만들어진 객체 역시 자기자신의 프로토타입을 갖는다. 이 새로운 객체의 원형을 이용하면 또 다른 새로운 객체를 만들어 낼 수도 있으며 이런 구조로 객체를 확장하는 방식을 프로토타입 기반 프로그래밍이라고 한다.

이 프로토타입 기반 프로그래밍은 Class 기반 OOP언어를 다루던 사람에게는 이해가 잘 안가는 부분 일 수도 있다. Class기반의 언어에서는 Class 안에 기술된 내용을 기반으로 인스턴스를 생성하여 객체를 사용한다. 하지만 자바스크립트는 Class가 존재하지 않는다. 그래서 자바스크립트에서는 객체의 원형인 프로토타입을 이용한 클로닝(Cloning:복사)과 객체특성을 확장해 나가는 방식을 통해 새로운 객체를 생성해 낸다.자바스크립트 응용에서 다룰 프로그래밍 패턴과 연관된 재밌는 사실중 하나는 자바스크립트의 프로토타입 객체의 확장은 옵져버패턴을 따른다.

### 자바스크립트에서 사용되는 프로토타입
자바스크립트에서 사용되는 프로토타입이란 용어는 크게 두가지로 나눈다. **Prototype Property**가 가리키고 있는 **Prototype Object**와 자기 자신을 만들어낸 객체의 원형을 의미하는 **Prototype Link**이다. 자바스크립트 관련 레퍼런스는 대부분 프로토타입에 대한 정의는 객체의 원형을 의미하는 **Prototype Link**로 정의 하지만 이 둘은 엄연히 틀린 의미이기 때문에 제대로 이해하고 있어야 한다.

### 자바스크립트의 프로토타입이란?
> 자바스크립트의 모든 객체는 자신을 생성한 객체 원형에 대한 숨겨진 연결을 갖는다. 이때 자기 자신을 생성하기 위해 사용된 객체원형을 프로토타입이라 한다. 자바스크립트의 모든 객체는 Object 객체의 프로토타입을 기반으로 확장 되었기 때문에 이 연결의 끝은 Object 객체의 프로토타입 Object이다.

어떠한 객체가 만들어지기 위해 그 객체의 모태가 되는 것을 프로토타입이라고 한다. Java나 PHP에서 말하는 class가 바로 자바스크립트의 프로토타입과 동일한 의미이다.

{% highlight javascript %}
function A(){};
var A = new A();
console.log( A );
{% endhighlight %}

위 예제는 new Operator를 사용하여 A라는 객체 생성자를 이용한 새로운 객체를 생성하여 B라는 변수에 담는 코드이다. 그렇게 만들어진 객체를 console.log를 찍어보면 아래와 같은 내용을 확인 할 수 있다.

![Prototype Example](/post_img/prototype_img1.jpg)

위 내용에서 주의깊게 봐야할 것이 있다. 바로  **_proto_** 와  **constructor** 이다. **\__proto__** 바로 이 녀석이 A라는 객체를 만들어내기 위해 사용된 객체원형에 대한 숨겨진 연결이다. 해당 객체의 프로토타입은 A라는 함수객체이며 이 객체의 생성자 역시 function A() 함수라는 것이다. 즉 **new Operator**를 통해 만들어진 객체는 function A() 를 자신의 프로토타입으로 사용하여 만들어졌다는 말이다.

하지만 "내가 알고 있는 프로토타입은 저게 아닌데?" 라고 생각하고 있을 것이다. 그 이유는 우리가 알고 있는 프로토타입은 자신을 만들어낸 객체원형을 의미하는 것이 아니고 prototype property이다.

### 자바스크립트의 프로토타입 프로퍼티란?
> 모든 함수 객체의 Constructor 는 prototype 이라는 프로퍼티를 가지고 있다. 이 prototype 프로퍼티는 객체가 생성될 당시 만들어지는 객체 자신의 원형이 될 prototype 객체를 가리킨다. 즉 자신을 만든 원형이 아닌 자신을 통해 만들어질 객체들이 원형으로 사용할 객체를 말한다. prototype object 는 default 로 empty Object를 가리킨다.

자바스크립트의 모든 객체는 생성과 동시에 자기자신이 생성될 당시의 정보를 취한 **Prototype Object** 라는 새로운 객체를 Cloning 하여 만들어 낸다.프로토타입이 객체를 만들어내기 위한 원형이라면 이 **Prototype Object** 는 자기 자신의 분신이며 자신ㅇㄹ 원형으로 만들어질 다른 객체가 참조할 프로토타입이 된다. 즉 객체 자신을 이용할 다른 객체들이 프로토타입으로 사용할 객체가 **Prototype Object** 인 것이다. 즉 위에서 언급한  **_proto_** 라는 prototype에 대한 link는 상위에서 물려받은 객체의 프로토타입에 대한 정보이며 prototype 프로퍼티는 자신을 원형으로 만들어질 새로운 객체들, 즉 하위로 물려줄 연결에 대한 속성이다.

{% highlight javascript %}
function foo(){};
var foo = new foo();
{% endhighlight %}

위 예제코드를 통해 만들어지는 Prototype Link와 Prototype Property가 가리키는 prototype Object의 Real Link에 대한 관계도는 다음과 같다.

![Prototype Example](/post_img/prototype_img2.png)

위 관계도에서 알수 있듯 모든 객체의 확장은 객체가 소유한 **prototype Object** 를 통해 이루어지며 이 연결의 끝은 Object 객체의  **prototype Object** 가 된다. 일반적으로 자바스크립트 코드를 통해 다루는 prototype 이 객체가 소유한 자기자신의 prototype Object 이기 떄문이다.

{% highlight javascript %}
function foo(x) {
    this.x = x;
};

var A = new foo('hello'); 
console.log(A.x);
//hello

console.log(A.prototype.x)
//syntax error
{% endhighlight %}

prototype 프로퍼티는  Constructor가 가지는 프로퍼티 이다. 그리고 함수객체만이 이 프로퍼티를 가지고 있다고 했다. A객체는 함수객체가 아니다. foo라는 원형을 이용하여 함수객체를 통해 만들어진 Object 객체에 확장된 단일 객체일 뿐이다. 즉 A는 prototype 프로퍼티를 소유하고 있지 않기에 A.prototype.x가 syntax error인 것이다. 즉 프로토타입을 이해하려면 foo.prototype.x.는 OK, A.prototype.x 는 error 라는 사실을 반드시 이해하고 이억하고 있어야 한다.

{% highlight javascript %}
var A = function () {
    this.x = function () {
         //do something
    };
};

//#예제 2.
var A = function () { };
A.prototype.x = function () {
    //do something
};
{% endhighlight %}

예제1의 this.x는 A의 프로퍼티 x이고 예제2는 A의 prototype Object 에 연결된 x라는 것을 이해할수 있을 것이다. 그럼 굳이 왜 prototype을 사용하여야 하는가? 이문제는 객체를 어떻게 사용 할 것인가에 따라 다르게 된다.

{% highlight javascript %}
//#예제 1.
var A = function () {
    this.x = function () {
         console.log('hello');
    };
};
A.x=function() {
    console.log('world');
};
var B = new A();
var C = new A();
B.x();
//hello
C.x();
//hello

//#예제 2.
var A = function () { };
A.x=function() {
    console.log('hello');
};
A.prototype.x = function () {
     console.log('world');
};
var B = new A();
var C = new A();
B.x();
//world
C.x();
//world
{% endhighlight %}

예제1, 예제2 에서 B,C 를 생성하기 위한 객체 원형 프로토타입은 A이다. 하지만 여기서 반드시 집고 넘어가야하는 사실은 B,C는 A를 프로토타입으로 사용하기 위해서 A의 prototype Object를 사용한다는 것이다. 그리고 이 prototype Object는 A가 생성될 당시의 정보만을 가지기 때문에 예제1에서 A의prototype Object가 알고 있는 x는 function(){console.log("hello");} 가 된다. 즉 A.x를 아무리 수정하여도 A의 prototype Object는 변경되지 않기 떄문에 A 를 프로토타입으로 생성되는 B,C는 function(){console.log("hello");} 만 참조하는 것이다.

예제2 에서의 결과가 world가 되는 이유도 같은 이유다. A.prototype은 A의 prototype Object를 참조하는 녀석이기 때문에 A.prototype.x를 정의한다는 것은 A의 prototype Object를 직접 이용하게 되는 것이고 그에 따라서 A의 prototype Object를 프로토타입으로 이용하여 만들어지는 B,C가 알고 있는 x는 function(){console.log("world")} 가 되는 것이다.

예제2의 객체 상속 모델을 그림으로 표현하면 아래와 같이 나온다.


![Prototype Example](/post_img/prototype_img3.png)

### 자바스크립트의 프로토타입 체인
> 객체의 생성 과정에서 모태가 되는 프로토타입과의 연결고리가 이어져 상속관계를 통하여 상위 프로토타입으로 연속해서 이어지는 관계를 프로토타입 체인 이라고 한다. 이 연결은 __proto__ 를 따라 올라가게 된다.

즉 프로토타입 체인이란 위에서 봤던 프로토타입을 상속해서 만들어지는 객체들간의 연관관계를 의미한다. 그림에서 __proto__ 프로퍼티들간 이어진 점선을 타고 가다보면 최종적으로 object 객체의 prototype object 에 다다르는 것을 알 수 있다. 그렇기 때문에 자바스크립트의 모든 객체는 object 객체에서부터 파생되어 나온 자식들이라고 하는것이다.

이러한 프로토타입 체인은 하위 객체에서 상위 객체의 프로퍼티와 메소드를 상속받는다. 그리고 동일한 이름의 프로퍼티와 메소드를 재정의 하지 않는 이상 상위에서 정의한 내용을 그대로 물려받는다. 하지만 여기에는 엄청난 꼼수가 숨어있다. 위 그림을 잘 보면 B 와 C 는 A prototype object 를 프로토타입으로 만들어졌음에도 불구하고 X 라는 프로퍼티가 존재하지 않는다. 사실 물려받는다 라는 말 자체가 꼼수인 것이다. 즉 하위 객체는 상위 객체의 속성과 메소드를 상속 받는 것이 아니라 그것을 공유 하고 있는 것이다.

{% highlight javascript %}
var A = function () { };
A.prototype.x = function () {
     console.log('hello');
};
var B = new A();
var C = new A();

B.x();
//hello 

C.x();
//hello 

A.prototype.x = function () {
     console.log('world');
};

B.x();
//world

C.x();
//world
{% endhighlight %}

위 예제에서 A의 prototype object 의 x메소드를 재정의 하였을 때 B, C 객체도 그 영향을 받는다는 것을 알 수 있다. 그 이유는 프로토타입 체인에 의한 공유 때문이다.

![Prototype Example](/post_img/prototype_img4.png)

여기서 유심히 봐야할 것은 A prototype object 가 소유한 Constructor 다. A prototype object의 Constructor 는 A객체의 생성자 함수 이다. 즉 이를 통해 만들어지는 객체들은 A객체가 생성될 당시 소유하고 있지 않는 x라는 메소드를 가질 수 없다. x는 A.prototype 을 이용하여 A prototype object 에 추가 되어진 메소드다. 하지만 x메소드는 프로토타입 체인에 의한 공유 메소드다. A 의 prototype object가 소유한 x 라는 메소드는 A prototype object 를 프로토타입으로 만들어진 모든 객체에서 사용할 수 있다. 이는 프로토타입 기반 상속에서 매우 중요한 특성을 말하고 있다.

위 예제에서 사용한 x라는 메소드를 하위 객체에서 찾지 못하면 상위 객체에서 해당 메소드를 탐색한다. 이러한 방법으로 프로토타입 체인을 따라 최상위 객체까지 도달할때 까지 해당 메소드를 찾지 못한다면 undefined 가 되는 것이다.

![Prototype Example](/post_img/prototype_img5.png)

하지만 A객체가 생성 당시 x라는 메소드 혹은 의미있는 프로퍼티에 대한 정의가 포함된 내용을 가지고 있다면 이들은 공유가 아닌 상속된다.

{% highlight javascript %}
var A= function() {};
var B = new A();
A.prototype.x='hello';
console.log(B);
{% endhighlight %}

![Prototype Example](/post_img/prototype_img6.jpeg)

{% highlight javascript %}
var A= function() {this.x='hello';};
var B = new A();
console.log(B);
{% endhighlight %}

![Prototype Example](/post_img/prototype_img7.jpeg)

이처럼 공유와 상속의 구분을 정확히 이해하고 사용하는 것은 매우 중요하다.이들을 햇갈리게 되면 후에 프로토타입 재정의에 따른 원하지 않는 결과를 줄 수 있기 때문이다.

