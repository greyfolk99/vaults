
```java
void addDoubles() {
	double a = 100.00000000000005;
	double b = 100.00000000000004;
	double expected = 200.00000000000009;
	System.out.println(expected != (a+b)) // ->
}
```

- 소수점 표현이 메모리 허용량을 넘을 경우 double은 정확하지 않을 수 있다.
- BigDecimal의 경우 절삭 또는 반올림 같은 정확한 계산식이 필요할 때 사용.

- 금융 계산 및 정밀 공학 계산 같이 어떠한 계산
