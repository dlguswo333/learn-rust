러스트 공부를 위한 기록용 리포지토리입니다.

# 리소스 참고 웹페이지
- https://edu.anarcho-copy.org/Programming%20Languages/Rust/rust-programming-language-steve-klabnik.pdf<br>
    러스트를 배우고자 하는 사람들은 한 번씩은 찾는다는 그 `The Rust Programming Language` 책의 PDF.
- https://javascriptweekly.com/link/120318/7b14d2bdbf<br>
    JS 개발자들을 위한 러스트 학습용 PDF. 166페이지로 짧은 시간 내에 부담 없이 배우기 좋을 듯 싶다.
- https://parksb.github.io/article/35.html<br>
    러스트에 대한 내용을 장점 위주로 한국어로 요약한 페이지.

# 러스트 설치하기
brew나 apt에서도 러스트를 설치할 수 있으나, 최신 버전 업데이트가 많이 느리고 러스트 공식 책에서도 권장하고 있지 않다.
리눅스와 맥에서는 아래와 같이 설치하도록 하자.

```bash
curl https://sh.rustup.rs -sSf | sh
```

윈도우에서는 <https://www.rust-lang.org/tools/install/> 링크로 들어가 다운로드 받도록 하자.
그런데 윈도우 환경에서는 컴파일하기 위해 Visual C++ Build Tools 툴체인을 추가적으로 요구하고 있다.
이런 점에서 러스트가 Golang보다는 상대적으로 약점을 보여주고 있다.

# 러스트와 같이 오는 프로그램들
러스트를 깔면 아래와 같은 프로그램도 깔리게 된다.

- rustup<br>
    러스트를 위한 툴체인 인스톨러다. nvm과 비슷하다.
- cargo<br>
    러스트의 빌드 시스템과 패키지 매니저 역할을 수행한다. npm과 비슷하다.
- rustc<br>
    러스트 컴파일러다. gcc와 비슷한데 cargo가 프로젝트 빌딩 역할을 수행하므로
    간단한 파일 하나 빌딩과 같이 경우 외엔 잘 쓰이질 않을 것이다.

# Expression과 Statement
Statement: 명령을 수행하며 자체적인 값을 가지지 않음.<br>
Expression: 자체적인 값을 가짐.

러스트에서는 Assignment Operation는 C계열 언어와 달리 Expression이 아니며,
Expression에 `;`을 붙이면 Statement가 된다.
러스트에서는 Curly Bracket 내부 맨 끝 줄에 Expression을 포함하면
해당 구문은 마지막 Expression을 반환하게 되며 (이는 다시 말해 블럭 구문이 Expression이 될 수 있다는 뜻이다),
if문도 Expression이 될 수 있다. 따라서 아래와 같은 소스 코드에서:

```rust
fn func() {
    let a={
        let tmp=0;
        tmp+1
    };
    let b=if 1==a {
        true
    } else {
        false
    };
    println!("{} {}", a, b);
}
```

`a`는 `1`이 되며, `b`는 `true`가 된다.<br>

# Macro
매크로란 컴파일 타임에 다른 코드로 바뀌는 인라인 코드이다.
러스트에서 매크로 함수는 뒤에 `!`가 붙는다.

# 변수
러스트에서 변수들의 변형을 막음으로써 러스트의 안전성과 병행성을 보장하기 위해
변수들은 기본적으로 **immutable**하다.

## let과 const
러스트에서 변수는 `let`과 `const` 이 두 가지로 구별되는데
두 가지 모두 기본적으로 immutable하다.

const가 let과 구별되는 특징은 다음과 같다.
- `const`는 무조건 immutable하다.<br>
    `let mut`과 같이 변수를 선언하면 변할 수 있는 변수를 만들 수 있다.
    그러나 const는 불가능하다.<br>
- `const`는 타입을 명시해야 한다.<br>
    let은 타입 추정으로 명시하지 않아도 되고 오히려 명시를 해주면
    경고를 띄우지만 const는 타입 명시가 필수적이다.
- `const`는 어느 스코프에도 선언이 가능하다.<br>
    const는 전역 범위에도 선언이 가능하지만 let은 불가능하다.
- `const`는 대문자로 선언해야 한다.<br>
    여느 다른 언어와 마찬가지로 const는 대문자로 선언해줘야 하며,
    소문자로 선언 시 경고를 내뱉는다.

## Shadowing
`let`은 기본적으로 immutable하지만 `mut` 키워드 없이 mutable한
변수를 만들 수 있다. 바로 똑같은 이름으로 재선언을 하는 것이다.

```rust
fn main() {
    let a=3;
    let a=a.to_string();
    println!("{}", a);
}
```

이를 Shadowing이라고 하는데, mut 키워드를 선언할 때와 달리
여전히 immutable한 특성을 유지하며, 변수의 타입 변경이 가능하다.

## 변수 타입
러스트는 Statically Typed Language이기에 당연히 데이터의 타입이 존재한다.
컴파일 타임에 모든 변수는 명시적으로든 암시적으로든 타입을 추정할 수 있어야 하며
그렇지 못할 시 에러가 발생한다.

여기부터 아래는 Scalar 타입이다.

### Integer
Integer는 소수점이 없는 숫자를 말한다. 러스트의 integer 타입은 간편하게도
unsigned signed에 따라 `u`와 `i`, 그리고 차지하는 비트 크기에 따라
뒤에 비트 크기가 붙는다. 러스트는 명시되지 않은 정수에 대해서
기본적으로 64bit 환경에서도 가장 빠른 i32를 사용한다.

추가로 `isize`와 `usize`라는 정수 타입은 실행 환경의 arch (32/64bit)에 따라
달라진다.

러스트에서 integer literal은 아래와 같은 포맷을 지원한다.

|리터럴|예시|
|---|---|
|10진수|12_345_678|
|16진수|0xff|
|8진수|0o77|
|2진수|0b1234-5678|
|바이트(`u8`만 지원)|b'A'|


### Floating Point
러스트에서 소수 타입은 `f32`, 그리고 `f64`가 존재한다.
기본적으로 러스트는 `f64` 타입을 사용한다.

### Boolean
러스트는 1바이트 크기의 `true` 그리고 `false`의 불린을 지원한다.

### Char
러스트에서는 곁따옴표의 String과 달리 Char는 홑따옴표를 사용하며 
**Unicode를 지원하기 위해 4바이트의 크기를 가진다**.
따라서 한국어와 함께 이모지도 러스트의 Char 변수에 넣을 수 있다.

***

이 다음은 Compound 타입이다.

러스트는 두 개의 원시 compound 타입을 가지고 있다: `tuple`과 `array`이다.

### Tuple
튜플은 여러 가지의 타입의 변수들을 하나의 변수로 묶을 수 있다.
튜플은 **고정된 길이**를 가지며 아래와 같이 선언하되,
타입 명시는 자유롭게 해도 좋고 안 해도 좋다.
또한 러스트의 튜플은 `destructuring`이 가능하다.
또는 인덱스로 접근하기 위해서는 `period`를 쓰면 된다.

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;
let five_hundred = tup.0;
```

위 프로그램에서 `x`는 `500`의 `i32` 타입의 변수가 되며
`five_hundred`도 마찬가지의 변수가 된다.

### Array
어레이는 튜플과 똑같이 고정된 길이를 가지며
차이점으로 달리 모든 엘리먼트의 타입이 같아야 한다.
어레이는 아래와 같이 선언할 수 있다.

```rust
let array = [1, 2, 3];
```

어레이는 스택 영역에 저장되며 고정된 리스트가 필요할 때 (e.g. 12달의 영어 표기)
유용하다.

어레이의 타입을 명시해주고 싶다면 아래와 같이 선언할 수 있다.
첫번째 인덱스에는 타입이, 두번째에는 어레이의 길이가 들어가면 된다.

```rust
let array: [i32, 3] = [1, 2, 3];
```

또한 만약 같은 값을 가진 변수로 어레이를 채우고 싶다면 아래와 같이
첫번째 인덱스에는 고정값, 두번째에는 어레이의 길이를 넣어주면 된다.

```rust
// array는 [3, 3, 3, 3, 3]이 된다.
let array = [3, 5];
```

어레이의 엘리먼트에 접근하고 싶다면 여타 다른 언어와 같이 `array[3]`으로 접근할 수 있다.
