
# Rust 기본 문법 테스트

### 함수
```rust
fn main() {
    println!("Hello, world!");
}
```

### 변수 종류 설명
- `let` 키워드로 변수를 선언한다.
- `mut` 키워드로 가변 변수를 선언한다.
- `const` 키워드로 상수를 선언한다.

```rust
fn main() {
    let x = 5;
    // x = 6; // error: cannot assign twice to immutable variable `x`
    let mut y = 5;
    y = 6; // ok
    const MAX_POINTS: u32 = 100_000;
    println!("x: {}", x);
    println!("y: {}", y);
    println!("MAX_POINTS: {}", MAX_POINTS);
}
```

let vs const(상수)
- `let`은 변수를 선언할 때 사용한다.
- `const`는 상수를 선언할 때 사용한다.
- 상수는 대문자와 밑줄로 구성되며, 밑줄을 사용하여 숫자를 가독성 있게 표현할 수 있다.
- 상수는 어디서나 선언될 수 있으며, 전역적으로 사용될 수 있다.
- 상수는 반드시 타입을 명시해야 한다.

- 둘다 기본적으로 불변이지만, `let`은 런타임에 값을 할당하고 `const`는 컴파일 타임에 값을 할당해야 한다.
- java로 치면 let = final, const = static final 정도로 이해하면 될 것 같다.

### 데이터 타입
- Rust는 정적 타입 언어이다.
- Rust는 변수의 타입을 추론할 수 있지만, 명시적으로 타입을 지정할 수도 있다.

```rust
fn main() {
    let guess: u32 = "42".parse().expect("Not a number!");
    println!("guess: {}", guess);
}
```

### Rust의 기본 데이터 타입
1. 스칼라 타입
    - 정수형: i8, u8, i16, u16, i32, u32, i64, u64, isize, usize
    - 부동소수점: f32, f64
    - 불리언: bool
    - 문자: char
```rust
fn main() {
    let x: i32 = 5;
    let y: f64 = 5.0;
    let z: bool = true;
    let a: char = 'a';
    println!("x: {}", x);
    println!("y: {}", y);
    println!("z: {}", z);
    println!("a: {}", a);
}
```

2. 복합 타입
    - 튜플: (i32, f64, u8)
    - 배열: [i32; 5]

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    let (x, y, z) = tup;
    println!("tup: {:?}", tup);
    println!("x: {}", x);
    println!("y: {}", y);
    println!("z: {}", z);
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    println!("arr: {:?}", arr);
}
```

3. 함수 타입
    - 함수의 타입은 함수의 매개변수와 반환값의 타입으로 구성된다.

```rust
fn main() {
    let f: fn(i32) -> i32 = plus_one;
    println!("f(5): {}", f(5));
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

4. 포인터 타입
    a. 레퍼런스 타입: &T
      - 불변 레퍼런스: &T
    b. 스마트 포인터 타입: Box<T>, Rc<T>, Arc<T>
    c. 원시 포인터 타입: *const T, *mut T
5. 기타 타입
    - 슬라이스 타입: &[T]
    - 문자열 타입: str
    - 유닛 타입: ()
    - 네버 타입: !
    - 동적 타입: dyn Trait