# Unit test
러스트에서 테스트는 `cargo test`
커맨드를 통해 가능하다.


# Unit test
러스트에서 유닛 테스트 코드 작성을 위해서는
유닛 테스트 코드 함수를 작성 후 위에
`#[test]`를 붙여주면 된다.

함수는 test 모드에서만 컴파일되며
테스트 함수는 아래 조건을 만족해야 한다.

- 함수 파라미터를 받지 말아야 한다.
- 리턴 타입은 `()`, 또는 `Result<(), E>`이어야 한다.

## Should panic
유닛 테스트 진행 시 올바른 인풋 값에 올바른 아웃풋을
내보내는 지 테스트도 중요하지만 올바르지 않은 인풋 값에
패닉을 함으로써 유저에게 값이 올바르지 않음을 알리는 것도
중요하다.

패닉해야 할 유닛 테스트 함수 위에 `#[should_panic]`을
붙여주면 패닉해야 할 함수를 테스트할 수 있다.

## Ignore test
`#[ignore]` 어트리뷰트가 붙은 테스트 코드는 테스트
실행 시 포함되지 않는다.

또한 어트리뷰트에 무시되는 이유를 첨부하는 것도 가능하다.

```rust
#[test]
#[ignore = "Not implemented yet"]
fn test() {
    assert_eq!(3, add(1, 2));
}
```

## Test module
만약 테스트 모듈을 만들어 코드를 분리하고 싶다면
아래와 같이 가능하다.

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod test_add {
    use super::*;

    #[test]
    fn add_positive() {
        assert_eq!(add(3, 4), 7);
    }

    #[test]
    fn add_negative() {
        assert_eq!(add(-2, -2), -4)
    }
}

fn main() {
    let a = 1;
    let b = 3;
    println!("{}", add(a, b));
}
```

`cfg`는 *Configuration Conditional Checks*의 약자로
cfg 어트리뷰트는 인자로 주어진 값을 컴파일 타임에 판별해
컴파일 여부를 결정한다. `cargo test`는 `test`
값을 `true`로 설정하므로 내부의 함수들이 컴파일되므로
실행되는 것이다.

모듈 내부의 `use super::*`는 모듈 외부의 `add` 함수를
모듈 내부로 로드하기 위해 필요하다. `add` 함수만 필요하니
`use super::add`로 바꾸어도 테스트는 정상 실행된다.

# Documentation test
러스트는 다큐멘테이션을 `///` 신택스를 통해 지원한다.
마크다운 문법을 지원하며 코드를 삽입할 수도 있는데,
라이브러리 crate (`cargo new --lib ...`)를 만들 시에만
다큐멘테이션 테스트가 가능하다.

```rust
/// Code Documentation...
/// Blah Blah Blah...
///
/// ```
/// let result = basic_math::add(2, 3);
/// assert_eq!(result, 5);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

`cargo test` 실행 시 다큐멘테이션 내부의 코드가 테스트로
실행되는 걸 확인할 수 있다.

# Integration test
러스트에서 Integration test도 library crate만 가능하다.
통합 테스트 코드의 특징은 구현 코드와 다른 디렉토리에 분리되어
선언된다는 점이다. 라이브러리 구현 코드를 `src` 폴더에
정의할 때 통합 테스트 코드는 `tests` 폴더 내에 저장된다.
테스트 실행 시 카고는 해당 폴더 내 소스 코드를 컴파일해
하나씩 실행한다.

> ⚠️ 통합 테스트 코드도 유닛 테스트 코드와 마찬가지로
> 함수 선언 위에 `#[test]` 어트리뷰트를 추가해야 한다.
