---
layout: post
title: "Namespace Pattern"
date: 2016-08-24
image: '/assets/img/dica-rapida-2/main.png'
description: 'JavaScript'
main-class: 'js'
color: '#EB7728'
tags:
- javaScript
- Namespace Pattern
- pattern
- 자바스크립트
- 네임스페이스 패턴
- 패턴
categories: javascript
twitter_text: ''
introduction: "전역변수가 많아질 수록 이름이 겹칠 우려가 있다. 뿐만 아니라 어느 곳에서든 접근할 수 있으므로 소스코드의 신뢰성을 떨어뜨릴 수 있다. 이러한 단점을 보완하기 위한 방법으로 네임스페이스 패턴이 있다."
---

전역변수를 기초로 하는 JavaScript의 단점 때문에 전역 네임스페이스의 오염문제는 많이 신경쓰이는 부분이다. 여러 스크립트가 한 페이지 안에 함꼐 있는 소스코드에서는 전역변수가 많아질 수록 이름이 겹칠 우려가 있다. 뿐만 아니라 어느 곳에서든 접근할 수 있으므로 소스코드의 신뢰성을 떨어뜨릴 수 있다. 이러한 단점을 보완하기 위한 방법으로 네임스페이스 패턴이 있다.

### 1. var 사용
JavaScript 에는 암묵적 전역이라는 개념이 있다. var를 사용하지 않고 변수를 선언하거나 선언되지 않은 변수를 사용하면 아무리 지역함수 내에 있더라도 전역에 속하게 된다. 정확히 말하면 전역변수가 아닌 전역객체의 프로퍼티로 생성된다.

{% highlight javascript %}
/*
 * a : 함수에 속하지 않고 var를 사용하여 선언된 전역 변수
 * b : 함수에 속하지 않고 암묵적으로 생성된 전역 객체의 프로퍼티
 * c : 함수에 속하고 암묵적으로 생성된 전역 객체의 프로퍼티
 */
 
var a = 1;
b = 2;
(function () { 
	c = 3; 
}());
 
delete a; 
delete b; 
delete c;
 
console.log(typeof a);        // number 출력
console.log(typeof b);        // undefined 출력
console.log(typeof c);        // undefined 출력
{% endhighlight %}

프로퍼티는 delete연산자로 삭제할 수 있지만 변수는 그렇지 않다는 특성을 통해 변수 b와 c는 전역변수가 아닌 전역객체의 프로퍼티가 됨을 알 수 있다.

### 2. 즉시실행 함수와 즉시 객체 초기화

전역변수를 줄이기 위해 즉시실행함수(IIFE Immediately-Invoked Function Expression)를 사용하는 방법이 있다. 이 함수는 선언과 동시에 바로 실행되므로 전역변수를 만들지 않아 플러그인이나 라이브러리 등을 만들때 사용되며 모듈패턴의 기반이 되기도 한다.

{% highlight javascript %}
console.log(typeof 
// 함수 선언문
function func() {
	var days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
	var today = new Date();
	var msg = 'Today is ' + days[today.getDay()] + ', ' + today.getDate(); 
	console.log(msg);  
}
);   // function 출력
{% endhighlight %}

{% highlight javascript %}
console.log(typeof 
// 즉시 실행 함수
(function () {
	var days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
	var today = new Date();
	var msg = 'Today is ' + days[today.getDay()] + ', ' + today.getDate(); 
	console.log(msg);  // Today is Mon, 24 출력
}())
);   // undefined 출력

{% endhighlight %}

위의 코드에서 typeof 키워드로 각 함수의 타입을 알아보면, 함수 선언문으로 선언된 위쪽의 코드의 함수 타입은 function이다. 이대로 전역범위에 선언하면 전역객체에 func 라는 함수가 추가된다. 반면 익명의 즉시실행함수 표현식으로 선언된 아래쪽 코드의 함수 타입은 undefined이다. 함수가 즉시 실행되고 난 후엔 전역에 남지않고 바로 사라지기 때문이다. 그렇기 때문에 재사용할 필요 없이 한번만 호출할 함수들은 즉시 실행 함수 패턴을 사용하면 전역 네임스페이스를 오염시키지 않는다.

즉시 객체 초기화라는 패턴도 있다. 괄호로 묶어서 바로 초기화 하는 사용방식과, 전역변수를 만들지 않는다는 장점이 즉시실행함수와 비슷하다. 단점으로는 대부분의 JavaScript 압축 도구가 즉시실행함수에 비해 효과적으로 압축하지 못한다는 점이 있다.

{% highlight javascript %}
({
	// 속성 정의
	name: "nextree",
 
	// 객체 메소드 정의
	getName: function () {
		return this.name;
	},
 
	// 초기화 메소드 정의
	init: function () {
		console.log(this.getName());   // nextree 출력
	}
}).init();
{% endhighlight %}

두가지 방법에서 고려해야할 사항으로, 즉시실행함수과 즉시객체초기화의 남용으로 인한 메모리 낭비이다.
JavaScript는 이렇게 할당 없이 정의만 할 경우, 전역 네임스페이스는 건드리지 않더라도, 전역 실행 컨텍스트의 temp=[] 내에 key-value를 추가하게 된다. 이 EC.temp 영역은 개발자가 접근할 수 없는 영역이며 스크립트 내의 다른 영역은 물론 어디에서도 접근할 수 없어 소스코드의 신뢰성에는 큰 도움이 된다. 하지만 같은 이유로 이 패턴을 남용하면 직접 관리할 수 없는 공간에 메모리가 계속 쌓이게 되기 때문에 신뢰성과 메모리의 문제를 함꼐 고민해서 이를 남용하지 않고 적절히 사용해야 한다.

### 3. 네임스페이스 패턴( Namespace Pattern )

#### Namespace Pattern이란?
네임스페이스는 구분이 가능하도록 정해놓은 범위나 영역을 뜻한다. 말 그대로 이름 공간을 선언하여 다른 공간과 구분하도록 한다. 객체나 변수가 겹치지 않는 안전한 소스코드를 만드는 개념이다. 하지만 JavaScript는 아직까지 네임스페이싱을 위한 기능을 지원하지 않기 때문에 다음의 특성을 통해 네임스페이스와 비슷한 효과를 얻을 수 있다.

* JavaScrip의 모든 객체는 프로퍼티를 가진다
* 그 프로퍼티는 다시 다른 객체를 담을 수 있다.

#### 3.1 객체 리터럴 네임스페이싱( Object Literal Namespacing )

가장 기본적인 네임스페이스 패턴으로 하나의 전역 객체를 만든 후 모든 함수, 객체, 변수를 여기에 추가하여 구현한다. 나중에 추가하지 않아도 전역 객체 선언 시에 리터럴로 미리 선언해 주어도 된다.

{% highlight javascript %}
// 하나의 전역 객체
var MYAPP = {};
 
MYAPP.Parent = function() { console.log('Parent'); };
MYAPP.Child = function() { console.log('Child'); };
 
MYAPP.variable = 1;
 
// 객체 컨테이너
MYAPP.modules = {};
 
// 객체들을 컨테이너 안에 추가합니다.
MYAPP.modules.module1 = {};
MYAPP.modules.module1.data = {a: 1, b: 2};
MYAPP.modules.module2 = {};
 
MYAPP.Parent();                               // Parent 출력
console.log(MYAPP.modules.module1.data.a);    // 1 출력
MYAPP.Child();                                // Child 출력
{% endhighlight %}

이 패턴은 코드 내에서 뿐 아니라 같은 페이지에 존재하는 JS라이브러리나 서드파티 코드와의 이름 충돌도 방지해 주며 체계적이라는 장점이 있다. 

* 하지만 단점도 존재한다.
	1. 모든 변수와 함수에 상위 객체 명을 모두 붙여야 하기 때문에 소스코드량이 늘어난다. 결국 그에 따라 다운로드 해야하는 파일의 크기도 늘어난다.
	2. 전역 인스턴스가 단 하나뿐이기 때문에 코드의 어느 한 부분이 수정되어도 전역 인스턴스를 수정하게 된다. 계속해서 나머지 기능들도 갱신된 상태를 물려받게 된다.
	3. 매번 객체에 접근하는데다, 이름이 중첩되고 길어지므로 검색이 느려지게 된다.

매번 오브젝트에 접근하는 단점을 해결하고 미래의 유지보수를 위하여 this 키워드를 사용하는 것을 생각할 수도 있다. this를 사용하면 전역객체에 접근하여 검색할 필요가 없이 바로 상위 객체만을 검색할 수 있기 때문이다.

하지만 namespace로 사용되고 있는 객체를 this를 사용하여 참조해서는 안된다.

{% highlight javascript %}
var MYAPP = {};
 
MYAPP.message = "Hi";
MYAPP.sayHello = function() {
	// 네임스페이스 명을 사용하여 리턴
	return MYAPP.message;
};
 
console.log(MYAPP.sayHello());    // Hi 출력
var direct = MYAPP.sayHello;
console.log(direct());            // Hi 출력
{% endhighlight %}

{% highlight javascript %}
var MYAPP = {};
 
MYAPP.message = "Hi";
MYAPP.sayHello = function() {
	// this를 사용하여 리턴
	return this.message;
};
 
console.log(MYAPP.sayHello());    // Hi 출력
var direct = MYAPP.sayHello;
console.log(direct());            // undefined 출력
{% endhighlight %}

위쪽 코드는 네임스페이스명을 사용하고, 아래쪽은 this키워드를 사용하여 같은 객체에 속한 변수를 리턴하고자 했다. 위쪽 코드는 네임스페이스명을 사용했을 때나 그를 새로운 전역변수에 대입했을 때나 같은 결과를 출력한다.하지만 아래쪽 코드는 네임스페이스명을 사용하여 출력하였을 때는 원하던 결과를 리턴하지만 그를 새로운 전역변수에 대입하자 undefined를 출력한다. 이는 this 키워드의 특성 때문인데 함수 영역 안에 있는 this키워드는 부모의 자식으로 불렸을 때에만 그 부모 객체를 가리키고 직접 호출하였을 때는 더이상 부모객체가 아닌 전역 객체를 가리키기 때문이다.

그래서 this는 절대 네임스페이스로 사용되고 있는 객체를 참조해서는 안된다. 위와 같이 네임스페이스로부터 식별자를 가져오는데 혼란이 될 수 있기 때문이다. 이러한 네임스페이스 패턴의 단점을 해결하기 위해서는 this키워드가 아닌 샌드박스 패턴을 사용해야 한다.


#### 3.2 범용 네임스페이스 함수

프로그램이 복잡해짐에 따라 코드의 각 부분들이 별개의 파일로 분리되어 선택적으로 문서에 포함되는 경우가 많다. 그러다 보면 네임스페이스로 사용할 객체를 선언할 때나 그 내부의 프로퍼티를 정의 할 때, 이미 있는 것을 재정의 하는 일도 생길 수 있다. 이를 확인 하지 못한 채 지나가면 내용을 덮어쓴ㄴ 문제가 생긴다.

{% highlight javascript %}
// 1번
var MYAPP = {};
 
// 2번
if (typeof MYAPP === "undefined") {
	var MYAPP = {};
}
 
// 3번
var MYAPP = MYAPP || {};
{% endhighlight %}

1번처럼 객체를 선언하는 대신, 2번처럼 MYAPP이 미리 선언되었는지를 확인하고 정의해 주어야 한다.
3번은 2번과 똑같이 작동하는 short-hand방식이다. 하지만 이런 식으로 반복해서 확인 작업을 해 주면 중복되는 내요으이 코드가 상당히 많이 생겨날 수 있다. 네임스페이스의 깊이가 깊어질 수록 각 단계 마다 확인해 주어야 하기 때문이다. 그래서 아래의 소스코드와 같이 이 작업을 맡아줄 재사용 가능한 함수를 만드는 것이 좋다.

{% highlight javascript %}
// 가장 상단에 위치할 객체는 먼저 선언합니다. 
// (namespace함수를 전역으로 선언하지 않기 위함입니다.)
var MYAPP = MYAPP || {};
 
MYAPP.nsFunc = function (ns_string) {
	// '.'으로 구분된 네임스페이스 표기를 쪼갭니다
	var sections = ns_string.split('.'),
	    parent = MYAPP,
	    i;
 
	// 최상단의 MYAPP객체는 이미 선언되었으므로 제거합니다.
	if (sections[0] === "MYAPP") {
		sections = sections.slice(1);
	}
 
	var s_length = sections.length;
	for (i=0; i<s_length; i+=1) {
		// 프로퍼티가 존재하지 않아야만 생성합니다.
		if (typeof parent[sections[i]] === "undefined") {
			parent[sections[i]] = {};
		}
		parent = parent[sections[i]];
	}
	return parent;
};
{% endhighlight %}

이렇게 만든 네임스페이스 함수를 사용하면 다음과 같은 긴 네임스페이스도 안전하게 만들 수 있다.