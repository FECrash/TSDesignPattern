![image](https://user-images.githubusercontent.com/48716298/203059378-7417303d-0b54-47da-a468-722c44a73e8c.png)
> 출처: [https://ithun.tistory.com/471](https://ithun.tistory.com/471)

<br/>

<details>
<summary>시작하기 전, 좋댓구알은 무슨 뜻일까요? 🤨 아시는 분은 채팅으로 작성해주세요!!!</summary>
<div markdown="1">

맞습니다! **좋아요, 댓글, 구독, 알림 설정** 의 줄임말입니다!
유튜버의 수익창출을 위해 많이 사용되는 말이죠!

![image](https://user-images.githubusercontent.com/48716298/203059741-e7834fc4-86af-4562-bfa2-5a327a1b5794.png)

> 출처: [https://www.youtube.com/watch?v=kOaW_A_kxGA](https://www.youtube.com/watch?v=kOaW_A_kxGA)

</div>
</details>

<br/>

옵서버 패턴은 유튜브의 **알림 설정**과 정말 유사합니다.

사용자는 유튜버를 구독하여 새로운 동영상이 업로드되면 **구독한 모든 사용자에게 알림을 전파**하고,
옵서버 패턴은 특정 객체를 구독하여 이벤트가 발생하면 **구독을 한 모든 객체에게 이벤트를 전파**합니다.

옵서버 패턴에선 **구독하는 주체를 Observer**라 하고, **구독 가능한 객체를 Observable**이라 합니다.
위의 문장을 다시 이야기 해보자면, Observable에서 이벤트가 발생하면 Observer에게 이벤트를 전파합니다.

그럼 예제와 함께 옵서버 패턴에 대해 자세히 알아봅시다!

## 예제

> 당신은 앱 내에서 일어나는 **사용자 인터렉션을 추적**하고 싶습니다.
>
> 1. 사용자가 버튼을 클릭하든, 텍스트에 마우스를 올리든 **타임스탬프를 로깅**하고 싶습니다.
> 2. 로깅된 값을 간단하게 **화면에 출력**해야 합니다. ~~(억지)~~

<img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/91456462-6d8f-4655-ac07-2d79cbb6c3ac/Nov-21-2022_03-38-02.gif?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221121%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221121T125151Z&X-Amz-Expires=86400&X-Amz-Signature=f25d190d3d919bc8139b4c023272aab8bfa6fa4a4c8af36fafbf4c17cc56ac31&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Nov-21-2022%252003-38-02.gif%22&x-id=GetObject" />

> https://github.com/areumsheep/TodayILearned/tree/main/design-pattern/observer

이런 경우에는 어떤 디자인 패턴을 사용하는 것이 좋을까요?
바로 **옵서버 패턴**입니다!

> **옵서버 패턴은 한 객체의 상태가 변경되어 다른 객체들을 변경해야 할 필요성이 생겼을 때**,    
> 그리고 실제 객체 집합들을 미리 알 수 없거나 이러한 집합들이 동적으로 변경될 때 사용하세요.

클릭 이벤트가 발생했을 때 화면에 간단하게 출력하고, 로깅해야 하기에 옵서버 패턴이 좋은 방법이 될 수 있습니다.

이제 옵서버 패턴을 직접 구현해봅시다!

### Observable 객체

Observable 객체는 보통 3가지 주요 특징을 포함합니다.

- `observers` : 이벤트가 발생할 때마다 전파할 Observer들의 배열
- `subscribe()` : Observer를 `observers` 배열에 추가한다.
- `unsubscribe()` : `observers` 배열에서 Observer를 제거한다.
- `notify()` : 등록된 모든 Observer들에게 이벤트를 전파한다.

코드로 구현하면 아래와 같이 구현할 수 있습니다.

```tsx
class Observable {
  #observers = [];

  subscribe(func) {
    this.#observers.push(func);
  }

  unsubscribe(func) {
    this.#observers = this.#observers.filter((observer) => observer !== func);
  }

  notify(data) {
    this.#observers.forEach((observer) => observer(data));
  }
}

export default Observable;
```

이제 Observable에 익숙해졌습니다.
예제의 구현사항을 어떻게 하면 구현할 수 있을까요?

### Observable 객체를 이용하여 구독하기

![image](https://user-images.githubusercontent.com/48716298/203060476-38731b0e-660d-4e64-8d1b-2c50e8498a90.png)
출처: https://patterns-dev-kr.github.io/design-patterns/observer-pattern/

코드로는 이렇게 표현할 수 있습니다.

```tsx
import Observable from './core/Observable.js';
import { $ } from './utils/dom.js';
import { logger, showLog } from './utils/logger.js';

// **1)** Observable 객체 생성한다.
const observable = new Observable();

// **2)** DOM에서 이벤트를 전파할 element를 가져온다.
const button = $('.button');
const p = $('.p');

// **3)** observable 객체에 구독할 함수를 추가한다.
observable.subscribe(logger);
observable.subscribe(showLog);

// **4)** observable을 이용하여 특정한 시점에 이벤트를 전파한다.
button.addEventListener('click', () => {
  observable.notify('Button Tag Click');
});
p.addEventListener('mouseover', () => {
  observable.notify('p Tag Mouse Over');
});
```

### 코드 전체보기

[https://github.com/areumsheep/TodayILearned/tree/main/design-pattern/observer](https://github.com/areumsheep/TodayILearned/tree/main/design-pattern/observer)
<img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/91456462-6d8f-4655-ac07-2d79cbb6c3ac/Nov-21-2022_03-38-02.gif?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221121%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221121T125151Z&X-Amz-Expires=86400&X-Amz-Signature=f25d190d3d919bc8139b4c023272aab8bfa6fa4a4c8af36fafbf4c17cc56ac31&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Nov-21-2022%252003-38-02.gif%22&x-id=GetObject" />

## 장/단점

### 단점

![image](https://user-images.githubusercontent.com/48716298/203060597-48496976-85c7-49b7-bdbe-04046c3c9a7b.png)   
출처: https://refactoring.guru/ko/design-patterns/observer

만약 아이폰14가 출시 되기 전, 사고 싶은 사람이 있다고 해봅시다.

왼쪽 사진에선 아직 출시가 되지 않았는데도 **가게에 직접 왔다갔다** 하며 확인을 하고 있습니다.
이럴 경우에는 아직 핸드폰이 가게에 존재하지 않기 때문에 **무의미한 방문**이 됩니다.

오른쪽 사진을 볼까요?
가게에서 새로운 제품이 출시될 때마다 **모든 사용자에게 스팸 문자 같은 핸드폰 출시 메세지**를 보내고 있습니다.
신제품에 관심이 없는 사람은 욕을 하며 화를 내겠지만, 관심이 있던 사람은 ‘앗싸!’ 하며 좋아하겠죠!

여기서 옵서버 패턴의 단점을 확인할 수 있습니다!

- **분명 필요없는 정보임에도 불구하고 함께 구독하고 있기에 불필요한 정보를 받을 수 있습니다.**
- Observer가 복잡해지면 모든 Observer들에 알림을 전파하는 데 **성능 이슈가 발생**할 수 있습니다.

### 장점

- Observer 객체는 Observable 객체와 강결합되어있지 않고 언제든지 분리될 수 있습니다.
  Observable 객체는 **이벤트 모니터링의 역할**을 갖고, Observer는 **받은 데이터를 처리하는 역할**을 갖게 됩니다.
  **관심사의 분리와 단일 책임의 원칙을 강제하기 위한 좋은 방법**입니다. \*\*\*\*
- 런타임에 객체 간의 관계들을 형성할 수 있습니다.
  - 알림을 실시간처럼 주고 받을수 있으니, 런타임에 관계를 만들수 있다는 의미 (Thanks for 욱창님)
  - 런타임에 객체가 변경될 때, 구독하고 있는 객체 모두 변경되어 관계가 변경된다는 의미, **유사 Redux**! (Thanks for 수경님)

## 출처

- [https://refactoring.guru/ko/design-patterns/observer](https://refactoring.guru/ko/design-patterns/observer)
- [https://patterns-dev-kr.github.io/design-patterns/observer-pattern/](https://patterns-dev-kr.github.io/design-patterns/observer-pattern/)
