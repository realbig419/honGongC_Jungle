## 요약

- 배열은 주소가 연속적인 데이터 모음
- 주소는 정수 연산 시 데이터의 형에 따라 자동으로 다음 데이터를 가리킴.
- 포인터는 주소를 담고 있음
- 따라서 포인터와 정수의 연산으로, 배열의 각 요소를 가리킬 수 있음
- 배열명은 배열의 첫 데이터의 주소를 가리키는 **포인터**임

  - 이 포인터가 가리키는 주소를 바꿀 수 없음(**상수**)
  - **배열명은 ‘포인터 상수(constant pointer)’** 임

    - `Constant Pointer` VS `Pointer to Constant`

      - **한국 번역은 논란이 있음- 자료 찾을 때 주의.**
        - 포인터 상수 = Constant Pointer
        - 상수(지시)포인터 = Pointer to Constant
        - ~~개인적으로는 ‘상수 포인터’와 ‘상수 지시 포인터’가 맞지 않나..~~
      - Constant Pointer - 포인터의 값이 상수
      - Pointer to Constant- 상수를 가리키는 포인터
      - Constant pointer to constant - 상수를 가리키는 constant 포인터

      ```c
      const int *ptr=&x; //pointer to Constant
      int *const ptr=&x; //constant pointer
      ```

      - pointer to constant
        - 포인터가 가리키는 **데이터는 변경할 수 없음**
        - 포인터가 가리키는 **주소는 변경할 수 있음**
      - constant pointer
        - 포인터의 **주소를 변경할 수 없음**
        - 포인터가 가리키는 데이터는 변경할 수 있음
        - **배열명은 constant pointer임**
          - 그래도 pointer와 크기는 다름.
          - 아래 코드도 안됨
            ```c
            int *const ptr = &{1,2,3,4,5};
            ```
          - 즉, 배열명은 constant pointer 의 일종이고
          - constant pointer를 코드를 통해 배열이랑 똑같이 만들수는 없다.
      - constant pointer to constant(상수 포인터 상수)
        ```c
        const int * const ptr = &x;
        ```
        - 값의 변경 불가능
        - 주소의 변경 불가능
      - 비교 예제

      ```c
      #include <stdio.h>

      int main() {
        int arr[5] ={1,2,3,4,5};
        int b;
        int *const ptr = arr;// constant pointer
        const int *pt = arr; //pointer to constant

        ptr=&b;//컴파일 에러
        *ptr=1;
        pt=&b;
        *pt=1;//컴파일 에러

        return 0;
      }
      ```

      - 그냥 const

      ```c
      //일반 변수의 경우에는 const 위치 상관 없다
      const int n;
      int const n; // 둘다 같음
      ```

- 포인터는 배열명 역할을 할 수 있지만, 배열의 크기까지 반영은 못한다.

## 배열과 포인터의 관계를 알아야 하는 이유

- 함수에 배열을 넘겨줄 때 포인터로 넘겨주는 이유를 알 수 있다.
- 연산 과정을 줄일 수 있다.
  ```c
  &ary[2] <-> ary+2
  *(pt+2) <-> pt[2]  //실제 연산과정이 줄지는 않고, 표현이 줄어듬
  ```

## 주소 연산

```c
주소 + 정수 -> 주소 + (정수 * 주소를 구한 변수의 크기)

int a = 10;
int *pa = &a;

pa = pa +1
pa+=1;
pa++;  //전부 가능

pa = pa -1 //뺄셈도가능

int b=20;
int *pb = &b;

c = *pb-*pa //도 가능. 배열에서 의미가 큼
```

## 배열과 포인터의 관계

- 배열의 **대괄호**는 포인터 연산의 (간접 참조, 괄호, 더하기) **연산 기능**을 갖는다.
  - 즉. **연산식**이다
  - 일반 포인터에 [] 연산식 적용 가능하다.
    ```c
    int a=10;
    int *pa=&a;
    printf("%d %d", pa[0], pa[1]); //가능
    ```
- 포인터는 배열명 역할을 할 수 있다.

```c
//a는 배열 이름, pa는 포인터일 때
pa = a;   <-> pa = &a[0];
a <-> &a[0]
a + i <-> &a[i]
a[i] <-> *(a+i)  //간접 참조, 괄호, 더하기
pa[i] <-> *(pa+i)
```

### **차이점:** **배열명 VS 포인터**

- sizeof 연산의 결과가 다르다.
  - 포인터는 포인터형의 size(시스템마다 다름)만 나옴
  - 배열은 배열이 확보한 총 크기가 나옴
  - (constant pointer로 배열을 정해주더라도 마찬가지)
- 포인터는 변수, 배열명은 (주소)상수

### 활용

- 포인터로 배열 요소 처리하기

```c
for(i=0;i<3;i++)
{
	printf("%d",*(pa++));
}
```

- 주의
  - 이후 사용시 포인터가 배열의 첫번째를 가리키지 않음에 유의
  - ++pa를 하면 다른 결과가 나옴.
- `++(*pa)` 와 `*++pa` (쉬움)
  - `++(*pa)` : 주소값에 있는 데이터를 증가
  - `*++pa` : 주소값이 증가된 곳에 있는 데이터

## 배열을 처리하는 함수

- 처리하기 위해 함수의 매개변수로 배열을 넘겨준다.
- 컴파일 시 자동으로 **배열명도 포인터로 변환**시킨다.
- 배열명도 포인터라 **복사하지 않고 접근해서 편하다**.
- **함수에 배열의 크기를 넘겨주는 방법은 파라미터로 따로 넘겨주는 방법 밖에 없다**.
  - 포인터 형태로 넘어오기에, 함수 안에서 size가 다르게 나온다.
  ```c
  sizeof(pa)/sizeof(pa[0])
  ```
  - 배열의 크기를 넘겨주지 않는 경우에, 보안 문제가 발생하기 쉽다. **배열의 크기를 같이 넘겨주는 습관을 들이자.**
- 아래 코드들은 모두 마지막 줄처럼 컴파일 된다

```c
void func(int pa[5]){} //뒤에 배열 크기는 아무 효과도 없음
void func(int pa[10]){}
void func(int pa[]){}  //배열이 넘어온다는 것을 알 수 있게 이걸로 쓰는 것이 좋다고 함.

void func(int *pa){}// 위 세 줄 다 이렇게 컴파일 됨
```

## 궁금한 것

- 배열의 크기는 절대 못 바꾸나? (사용자 입력에 따라 조정 등)
  - 컴파일 시간에 확정되므로 실행하는 동안 변경할 수 없다.
  - 크기 조정 가능 자료구조는 ‘동적 배열’ 또는 ‘연결 리스트’를 이용해야 한다.
- 배열의 형과, 크기는 어디에 저장되는 걸까?
  - 저장되지 않는다. type이나 sizeof() 연산은 컴파일 시간에 처리된다.
  - 실행시간에 처리되지 않는다.
- 배열에 const를 쓰면?

  - 배열의 값들이 변하지 않는다.
  - 함수를 통과하면 포인터로 접근이 되기 때문에, 변경할 수 있다.

  ```c
  #include <stdio.h>

  void func(int *);

  int main() {
    const int ptr[] = {1, 2, 3, 4, 5};
    printf("%d, %d\n", ptr[0], ptr[1]);
    //ptr[0] = 10; 컴파일 에러

    func(ptr);  //컴파일 경고
    printf("%d, %d\n", ptr[0], ptr[1]);

    return 0;
  }

  void func(int ptr[])
  {
    ptr[0] = 10;
  }
  ```
