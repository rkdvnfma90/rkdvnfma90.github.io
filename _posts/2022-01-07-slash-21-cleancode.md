---
title: [SLASH 21 - 실무에서 바로 쓰는 Frontend Clean Code]
date: 2022-01-07 19:12:00 +0900
categories: [웹 성능 최적화]
tags: [클린코드]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1631965830/noticon/sulndtevazxyzuprippz.png
  width: 100
  height: 100
  alt: Clean Code
---

> 해당 글은 [SLASH 21 - 실무에서 바로 쓰는 Frontend Clean Code](https://www.youtube.com/watch?v=edWbHp_k_9Y&t) 영상을 보고 정리한 글 입니다.

<br/>

## 안일한 코드 추가의 함정

유지보수할 때 안일한 코드 추가의 함정에 대해 알아보자

<br/>

### 상황

- 기능 추가 요청이 들어왔다
- 추가 요구사항은 다음과 같다
  - 보험에 대한 질문을 입력하는 페이지가 있는데 `내 설계사`가 있는 경우에는 설계사 사진이 들어간 팝업을 먼저 띄워주세요

![image](https://user-images.githubusercontent.com/52060742/148495277-370095dc-f6e9-4529-b5d9-0f2c10ba3b32.png)


<br/>

### 기존 코드 파악

```jsx
const QuestionPage = () => {
  const handleQuestionSubmit = async () => {
    const 약관동의 = await 약관동의_받아오기();

    if (!약관동의) {
      await 약관동의_팝업열기();
    }

    await 질문전송(questionValue);

    alert('질문이 등록되었습니다.');
  };

  return (
    <main>
      <form>
        <textarea placeholder='어떤 내용이 궁금한가요?' />
        <button onClick={handleQuestionSubmit}>질문하기</button>
      </form>
    </main>
  );
};

export default QuestionPage;
```

- 질문하기 버튼을 클릭하면 유저의 약관동의 여부를 검사하고 필요하다면 팝업을 띄운다
- 질문을 전송하고 성공 Alert를 띄운다

**이 코드에 새 기능을 어떻게 추가하면 될까?**

클릭함수에서 `내 설계사`가 있으면 팝업 띄우는 로직을 추가하고 팝업 컴포넌트를 추가하면 된다.

<br/>

```jsx
const QuestionPage = () => {
  // 팝업 Open 여부 상태 추가
  const [popupOpened, setPopupOpened] = useState(false);

  const handleQuestionSubmit = async () => {
    // 내설계사를 검사하는 조건문 추가
    const 내설계사 = await 내설계사_받아오기();

    if (내설계사 !== null) {
      setPopupOpened(true);
    } else {
      const 약관동의 = await 약관동의_받아오기();

      if (!약관동의) {
        await 약관동의_팝업열기();
      }
    }

    await 질문전송(questionValue);
    alert('질문이 등록되었습니다.');
  };

  // 팝업내 확인 버튼 핸들러 추가
  const handleMyExpertQuestionSubmit = async () => {
    await 내설계사_질문전송(questionValue, 내설계사.id);
    alert(`${내설계사.name}에게 질문이 등록되었습니다.`);
  };

  return (
    <main>
      <form>
        <textarea placeholder='어떤 내용이 궁금한가요?' />
        <button onClick={handleQuestionSubmit}>질문하기</button>
      </form>

      {/* 팝업 컴포넌트 추가 */}
      {popupOpened && <내설계사팝업 onSubmit={handleMyExpertQuestionSubmit} />}
    </main>
  );
};

export default QuestionPage;
```

<br/>
<br/>


### 요구사항 추가 후 문제점

이렇게 요구사항이 추가된 코드를 봤을 때 타당하고 자연스러운 코드 추가처럼 보이지만 `나쁜코드`가 되었고 그 이유에 대해 살펴보자.

<br/>

**1. 하나의 목적인 코드가 흩어져 있다.**


`내 설계사`팝업 관련된 코드는 아래 사진과 같이 표시된 부분이다.

![image](https://user-images.githubusercontent.com/52060742/148500978-e860cf82-d57c-42e4-b2aa-a294fb273168.png)



이것들이 다 떨어져 있기 때문에 나중에 또 다른 기능을 추가할 때 스크롤을 위아래로 이동하며 로직을 살펴봐야 한다.

<br/>

**2. 하나의 함수가 여러가지 일을 하고 있다**

기존에 있던 함수 `handleQuestionSubmit`함수가 3가지 일을 하고 있다. 그래서 세부 구현 내용을 모두 읽어야 이 함수의 역할을 알 수 있게 된다, 이렇게 되면 나중에 코드를 추가하거나 삭제할 때 시간이 더 걸리게 될 것이다.

![image](https://user-images.githubusercontent.com/52060742/148501261-69f517de-c680-4187-9874-a6c66ed3068a.png)

<br/>

**3. 함수의 세부 구현 단계가 제각각이다**

그림에 표시된 두 함수는 이벤트 핸들링 함수이다. 함수의 이름도 각각 `handleQuestionSubmit`, `handleMyExpertQuestionSubmit`로 비슷하다.

![image](https://user-images.githubusercontent.com/52060742/148501374-2d189318-eaa3-42d8-b53e-8d4893d6819f.png)

하지만 첫 번째 함수는 질문전송 외에 여러가지 일을 하고 있기 때문에 읽기 어지럽다. 그래서 코드를 이상하게 지레짐작할 수 있다.

<br/>
<br/>

> 그 때는 맞고 지금은 틀리다

처음 깔끔했던 코드와 다르게 작은 기능을 추가했더니 어지러운 코드가 되어버렸다. 🤪😵

여기서의 함정은 `Pull Request`에서 봤을 땐 이것이 어지러운 코드라는 걸 파악하기 어려웠을 것이다. 변경점 자체에는 틀린 점이 없기 때문이다. 하지만 코드 전체를 본다면 엉망인 코드이다.

<br/>
<br/>

### 리팩토링 하기

**1. 함수의 세부 구현 단계 통일**

기존 `handleQuestionSubmit`함수의 이름을 `handleNewExpertQuestionSubmit`로 변경후 `새로운 설계사`에게 질문하는 로직만 넣어 `handleMyExpertQuestionSubmit`함수와 세부 구현 단계를 통일했다

<br/>

**2. 하나의 목적인 코드는 뭉쳐 두기**

기존에는 팝업을 여는 버튼과 팝업 코드가 떨어져있었는데 팝업 관련 코드를 하나로 합쳤다 (`PopupTriggerButton` 컴포넌트)

<br/>

**3. 함수가 한 가지 일만 하도록 쪼개기**

약관동의 관련 함수를 쪼개서 필요한 시점에 부르도록 하였다

<br/>
<br/>

**리팩토링 된 코드**

```jsx
... 생략

const handleNewExpertQuestionSubmit = async () => {
  await 질문전송(questionValue);
  alert('질문이 등록되었습니다.');
};

const handleMyExpertQuestionSubmit = async () => {
  await 내설계사_질문전송(questionValue, 내설계사.id);
  alert(`${내설계사.name}에게 질문이 등록되었습니다.`);
};

const openPopupToNotAgreedUser = async () => {
  const 약관동의 = await 약관동의_받아오기();

  if (!약관동의) {
    await 약관동의_팝업열기();
  }
};

return (
  <main>
    <form>
      <textarea placeholder='어떤 내용이 궁금한가요?' />

      {내설계사.connected ? (
        <PopupTriggerButton
          popup={<내설계사팝업 onButtonSubmit={handleMyExpertQuestionSubmit} />}
        >
          질문하기
        </PopupTriggerButton>
      ) : (
        <button
          onClick={async () => {
            await openPopupToNotAgreedUser();
            await handleNewExpertQuestionSubmit();
          }}
        >
          질문하기
        </button>
      )}
    </form>
  </main>
);

... 생략
```

리팩토링 된 코드가 조금 더 길어졌다. 그 이유는 `클린코드는 짧은 코드가 아니라 원하는 로직을 빠르게 찾을 수 있는 코드이기 때문`이다.


<br/>
<br/>

## 로직을 빠르게 찾을 수 있는 코드

원하는 로직을 빨리 찾기 위해선 다음과 같은 단계가 필요하다

1. 응집도 - 하나의 목적을 가진 코드를 모은다.
2. 단일책임 - 함수가 하나의 일만 하도록 한다.
3. 추상화 - 함수의 세부구현 단계가 제각각 일 때는 추상화 단계를 조정해서 핵심 개념을 필요한 만큼 노출해야 한다.

<br/>

### 응집도

같은 목적인 코드는 뭉쳐두자!


```jsx
const QuestionPage = () => {
  // 팝업 조작 1
  const [popupOpened, setPopupOpened] = useState(false);

  const handleClick = () => {
    setPopupOpened(true);
  };

  // 팝업 조작 2
  const handlePopupSubmit = async () => {
    await 질문전송(연결전문가.id);
    alert('질문을 전송했습니다.');
  };

  return (
    <>
      <button onClick={handleClick}>질문하기</button>

      {/* 팝업 조작 3 */}
      <Popup title='보험 질문하기' open={popupOpened}>
        <div>전문가가 설명해드려요</div>
        <button onClick={handlePopupSubmit}>확인</button>
      </Popup>
    </>
  );
};

export default QuestionPage;
```

팝업을 조작하는 코드가 이렇게 세 군데에 서로 떨어져 있다. 그렇기 때문에 파악도 한 번에 안되고 버그 발생 위험이 높은 코드이다.

<br/>
<br/>

**리팩토링 v1**

```jsx
const QuestionPage = () => {
  const [openPopup] = useMyExpertPopup(연결전문가.id);

  const handleClick = () => {
    setPopupOpened(true);
  };

  return <button onClick={handleClick}>질문하기</button>;
};

export default QuestionPage;
```

커스텀 훅을 사용해서 한 군데로 뭉쳤기 때문에 `openPopup` 함수만 호출하면 팝업을 열 수 있게 되었다. 하지만 오히려 읽기 힘든 코드가 되었다. 어떤 내용의 팝업을 띄우는지, 팝업에서 버튼을 눌렀을 때 어떤 액션을 하는지가 이 페이지에서 가장 중요한 포인트 인데 커스텀 훅 속에 가려져서 한 번에 알 수 없게 되었다.

이것이 커스텀 훅의 대표적인 `안티패턴`이다.

그렇다면 무엇을 뭉쳐야 좋은 코드가 되는 걸까? 🤔

- 뭉치면 쾌적한 코드
  - 당장 몰라도 되는 디테일
  - 이것을 숨겨둔다면 짧은 코드만 보고도 빠르게 코드의 목적을 파악하는게 쉬워진다

- 뭉치면 답답한 코드
  - 코드 파악에 필수적인 핵심 정보
  - 이를 분리해 두면 여러 모듈을 넘나들며 흐름을 따라가야 하기 때문이다

`뭉쳐서 짧은 코드로 만든다고 코드가 깨끗해지는 것은 아니다!`

<br/>

**코드 응집 TIP!**

핵셈 데이터와 세부 구현나누기

```jsx
const QuestionPage = () => {
  // 세부 구현 1 - 팝업을 열고 닫을 때 사용하는 상태
  const [popupOpened, setPopupOpened] = useState(false);

  const handleClick = () => {
    setPopupOpened(true);
  };

  // 핵심 데이터 1 - 팝업 버튼 클릭 시 수행하는 액션
  const handlePopupSubmit = async () => {
    await 질문전송(연결전문가.id);
    alert('질문을 전송했습니다.');
  };

  return (
    <>
      <button onClick={handleClick}>질문하기</button>

      {/* 핵심 데이터 2 - 팝업의 제목, 내용 */}
      {/* 세부 구현 2 - 컴포넌트의 세부 마크업, 팝업 버튼 클릭 시 특정 함수를 호출하는 바인딩  */}
      <Popup title='보험 질문하기' open={popupOpened}>
        <div>전문가가 설명해드려요</div>
        <button onClick={handlePopupSubmit}>확인</button>
      </Popup>
    </>
  );
};

export default QuestionPage;
```

여기서 핵심 데이터만 남기고 세부 구현을 숨기면 파악하기 쉬운 코드가 된다.

**리팩토링 v2**

핵심 데이터는 밖에서 전달하고, 나머지는 뭉친다.

```jsx
const QuestionPage = () => {
  const [openPopup] = usePopup();

  const handleClick = async () => {
    // 핵심 데이터 2 - 팝업의 제목, 내용
    const confirmed = await openPopup({
      title: '보험 질문하기',
      contents: <div>전문가가 설명드려요</div>,
    });

    if (confirmed) {
      // 핵심 데이터 1 - 팝업 버튼 클릭 시 수행하는 액션
      await submitQuestion();
    }
  };

  const submitQuestion = async 연결전문가 => {
    await 질문전송(연결전문가.id);
    alert('질문을 전송했습니다.');
  };

  return <button onClick={handleClick}>질문하기</button>;
};

export default QuestionPage;
```

`usePopup` 커스텀 훅 안에 모든 코드를 다 숨기는 것이 아니라 `세부 구현`만 숨겨놓고 핵심 데이터인 `팝업 제목, 내용, 액션`은 밖에서 넘긴다. 그러면 세부 구현을 읽지 않고도 어떤 팝업인지 파악할 수 있다.

<br/>
<br/>

> 팝업, 너에게 선언한다! 제목은 `보험 질문하기`, 내용은 `전문가가 설명드려요`, 확인버튼 클릭하면 `질문을 제출`해라!

<br/>
<br/>

라고 선언하면 팝업이 이미 해둔 세부 구현을 바탕으로 해당 내용을 뿌려준다. 이것을 `선언형 프로그래밍`이라고 한다 (핵심 데이터만 전달받고 세부 구현은 뭉쳐 숨겨 두는 개발 패턴)

이 패턴의 특징은 `무엇`을 하는 함수인지 빠르게 이해가 가능하고, 세부 구현은 안쪽에 두웠기 때문에 신경쓸 필요가 없고, 마지막으로 `무엇`만 바꿔서 쉽게 재사용이 가능하다.

이 패턴과 상반되는 개념으로는 `명령형 프로그래밍`이 있다. 이것은 `어떻게` 해야 할지 하나하나 명령하여 코드를 작성하는 패턴이다. `선언형 프로그래밍`도 내부는 명령형 프로그래밍으로 되어있다.

세부 구현이 모두 노출되어 있기 때문에 이를 커스텀하기 쉽지만 읽는 데 오래 걸리고 재사용하기 어렵다.

<br/>
<br/>

**Q. 그럼 선언형 프로그래밍으로 작성된 코드가 무조건 좋은가?**
- A. 아니다. 두 방법을 유동적으로 사용하면 된다.

<br/>
<br/>

### 단일책임


**하나의 일을 하는 뚜렷한 이름의 함수를 만들자**

함수의 이름을 정할 때 중요 포인트가 담겨있지 않은 함수명은 읽는 사람이 예상한 대로 코드가 동작하지 않기 때문에 코드에 대한 `신뢰 하락`으로 이어진다. 그 다음부터는 함수명을 믿지 못하게 된다.

<br/>

**리액트 컴포넌트로 기능을 분리할 수도 있다.**

- 버튼을 클릭하면 서버에 로그를 찍는 코드

Before
```jsx
<button onClick={async () => {
  log('제출 버튼 클릭');
  await openConfirm();
}}>
```

버튼 클릭 함수에 로그 찍는 함수와 API를 호출하는 함수가 섞여 있다.

</br>

After
```jsx
<LogClick message='제출 버튼 클릭'>
  <button onClick={openConfirm}>
</LogClick>
```

로그는 버튼을 감싼 컴포넌트에서 찍고, 버튼 클릭함수에서는 API 호출만 신경쓴다.

<br/>
<br/>

- IntersectinObserver 

Before
```jsx
const targetRef = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver(([{ isIntersecting }]) => {
      if (isIntersecting) {
        fetchSomething(nextPage);
      }
    });

    return () => {
      observer.unobserve(targetRef.current);
    };
  }, []);
```

이 코드는 `옵저버 코드 세부구현`과 `API 호출`로직이 섞여 있어 조금 아쉬운 코드이다.

<br/>

After
```jsx
<IntersectionArea onImpression={() => fetchSomething(nextPage)}>
  <div>더 보기</div>
</IntersectionArea>
```

`옵저버 코드 세부구현`은 IntersectionArea 컴포넌트에 숨겨두고 사용하는 입장에서는 `API 호출`만 신경쓴다.

<br/>
<br/>

- 조건이 많아지면 한글 변수명도 유용하다.
  - 도메인이 복잡해서 영어 이름 길게 짓는 게 오히려 복잡도를 높일 때
  - 상수를 직관적으로 보고 싶을 때
  - 복잡한 조건문이 많아질 때
  - 마치 주석을 달아둔 것과 같은 효과도 낸다.
  - 코드가 약간 귀엽다(?) 😄

<br/>
<br/>

### 추상화

Before
```jsx
<div style={{팝업스타일}}>
  <button onClick={async () => {
    const res = await 회원가입();

    if (res.success) {
      프로필로이동();
    }
  }}>
    전송
  </button>

</div>
```

팝업 컴포넌트 코드를 제로부터 디테일 하게 구현

</br>

After
```jsx
<Popup
  onSubmit={회원가입}
  onSuccess={프로필로이동}
/>
```

`제출액션`, `성공액션`이라는 중요한 개념만 남기고 나머지를 추상화 했다.

<br/>
<br/>

그럼 어느 단계까지 추상화를 해야 할까?? 구체적인 코드를 조금 추상적이게, 혹은 더욱 추상적이게 리팩토링 할 수 있다.

**Level 0**

```jsx
<button onClick={showConfirm}>
  전송
</button>

{isShowConfirm && (
  <Confirm onClick={() => {showMessage('성공')}}>
)}
```

버튼을 클릭하면 컨펌 창을 띄우고, 여기서 컨펌 버튼을 클릭하면 특정 메시지를 띄우는 구체적인 코드가 있다.

<br/>
<br/>

**Level 1**

```jsx
<ConfirmButton onConfirm={() => showMessage('성공')}>
  전송
</ConfirmButton>
```

버튼을 눌렀을 때 컨펌창을 띄우는 기능을 `ConfirmButton` 컴포넌트로 추상화 했다. `onConfirm`을 통해 개발자가 원하는 액션을 넘길 수 있다.

<br/>
<br/>

**Level 2**

```jsx
<ConfirmButton message="성공">
  전송
</ConfirmButton>
```

message라는 prop만 넘겨서 컨펌창에 원하는 메시지를 보여주도록 더 간단하게 추상화 할 수 있다.

<br/>
<br/>

**Level 3**

```jsx
<ConfirmButton />
```

더 나아가 모든 기능을 `ConfirmButton` 컴포넌트로 추상화 시킬수도 있다.

<br/>
<br/>

**정답은 없다! 상황에 따라 필요한 만큼 추상화 하면 된다.**

하지만 `추상화 수준(레벨)`이 한 코드파일 안에서 섞여 있으면 코드 파악이 오히려 어려워 질 수 있다.

```jsx
// 높은 추상화
<Title>별점을 매겨주세요</Title>

// 낮은 추상화
<div>
  {STARS.map(() => <Star />)}
</div>

// 높은 추상화
<Review /> 

// 중간 추상화
{rating !== 0 && (
  <>
    <Agreement />
    <Button rating={rating}>
  </>
)}
```

<br/>
<br/>

## 앞으로 우리가 해야할 행동

1. 담대하게 기존 코드 수정하기
  - 두려워하지 말고 기존 코드를 씹고 뜯고 맛보고 즐기자
  - 구조 뜯기를 두려워 하면, 클린한 실무 코드를 유지할 수 없다

2. 큰 그림 보는 연습하기
  - `그 때는 맞고 지금은 틀릴 수 있다`라는 것을 생각하자
  - 기능 추가 자체는 클린해도, 전체적으로는 어지러울 수 있다
  - 기존에 깨끗하던 코드에 내가 기능을 추가하면서 망쳐놓을 수 있다

3. 팀과 함께 공감대 형성하기
  - 코드에 정답은 없다
  - 명시적으로 이야기를 하는 시간이 필요하다

4. 문서로 적어보기
  - 글로 적어야 명확해진다
  - 향후 어떤 점에서 위험할 수 있는지
  - 어떻게 개선할 수 있는지