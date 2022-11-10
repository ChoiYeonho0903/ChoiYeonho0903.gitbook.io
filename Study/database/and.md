# 정규화 & 비정규화

### 1. 이상현상 (Anomaly)

💡 테이블에서 일부 속성들의 종속이나 데이터의 중복으로 인해 데이터 조작시 불일치가 발생하는 것

#### (1) 삽입 이상 (Insertion Anomaly)

💡 불필요한 정보를 함께 저장하지 않고는 어떤 정보를 저장하는 것이 불가능한 경우

* 불필요한 정보 (NULL)이 들어가야 한다.

#### (2) 갱신 이상 (Update Anomaly)

💡 반복된 데이터 중에 일부를 갱신 할 시 데이터의 불일치가 발생

#### (3) 삭제 이상 (Deletion Anomaly)

💡 필요한 정보를 함께 삭제하지 않고는 어떤 정보를 삭제하는 것이 불가능

### &#x20;

### 2. 정규화

💡 관계형 데이터베이스 설계 시 데이터의 중복을 줄이고, 이상 현상을 최소화하기 위해 데이터를 구조화하는 작업

* 한 테이블에 정보가 중복 저장되는 경우
  * 저장 공간 낭비
  * 갱신, 삽입, 삭제 이상 발생
* 정규화를 통해 데이터의 중복을 줄인다.
  * DB 저장 용량을 효율적으로 관리할 수 있다.
  * 무결성을 유지할 수 있다.
* 정규화의 3가지 원칙
  * 정보의 무손실 : 분해된 릴레이션이 표현하는 정보는 분해되기 전의 정보를 모두 포함해야 한다.
  * 최소 데이터 중복 : 이상 현상을 제거, 데이터 중복을 최소화
  * 분리의 원치 : 하나의 독립된 관계성은 하나의 독립된 릴레이션으로 분리해서 표현

<figure><img src="https://blog.kakaocdn.net/dn/cyfmOt/btrQw3yv9RY/ACSKY0NKuOiAmYjLbNDLCk/img.png" alt=""><figcaption></figcaption></figure>

#### (1) 제1정규형

💡 테이블의 컬럼이 더 이상 분해될 수 없는 원자값만을 가진다.

<figure><img src="https://blog.kakaocdn.net/dn/bD0ka4/btrQvZiKo1X/F9OxGdU63ARYoPG9qZQkZK/img.png" alt=""><figcaption></figcaption></figure>

* 테이블의 모든 속성은 원자값을 가져야한다.
* 새로운 row를 생성하여 원자값을 만들어준다.

#### (2) 제2정규형

💡 제1 졍규형을 만족하고, 부분 함수 종속성을 제거하여 테이블의 모든 컬럼이 완전 함수 종속을 만족하도록 만든다.

* 기본키가 2개 이상으로 이루어졌을 때만 고려한다.

<figure><img src="https://blog.kakaocdn.net/dn/Dak91/btrQtRr4dHb/JhKrfVo6WlBkNbDTbgWbz1/img.png" alt=""><figcaption></figcaption></figure>

* 기본키는 주문번호 + 상품코드
* 주문일자,회원번호,회원이름은 주문번호에 종속적이다. ⇒ 부분 함수 종속
* 상품명은 상품코드에 종속적이다. ⇒ 부분 함수 종속
* 주문 수량은 주문번호 + 상품코드에 종속적이다. ⇒ 완전 함수 종속
* 하나의 테이블에 부분 함수 종속과 완전 함수 종속이 혼재되어 있다.
* 모든 컬럼이 완점 함수적 종속되게 정규화를 해야한다.

<figure><img src="https://blog.kakaocdn.net/dn/bgr40r/btrQr6JUEXG/m4VdWhi1WnP8Lwjkd5EJL0/img.png" alt=""><figcaption></figcaption></figure>

* 제2정규화를 통해서 모든 컬럼이 완전 함수 종속을 만족하게 되었다.

#### (3) 제3정규형

💡 제2 정규형을 만족하고, 기본키가 아닌 모든 속성이 기본키에 이행적 함수 종속되지 않도록 릴레이션을 분해한다.

* A → B, B → C 이면 A → C 가 성립하는데 이때 집합 C가 집합 A에 이행적으로 함수 종속이 되었다고 한다.

<figure><img src="https://blog.kakaocdn.net/dn/TnJim/btrQr2gwMju/Kur7sgrrpzSl20mINzMBkk/img.png" alt=""><figcaption></figcaption></figure>

* 기본키는 주문번호 (1개)
* 주문번호 → 회원번호 → 회원이름
  * 회원번호(B)는 주문번호(A)에 종속적이다.
  * 회원이름(C)은 회원번호(B)에 종속적이다.
  * 그러므로 회원이름(C)는 주문번호(A)에 종속적이다. ⇒ 이행적 함수 종속

<figure><img src="https://blog.kakaocdn.net/dn/s9s8H/btrQsGc8esv/sGuLmIOhRsk9F254kXAe81/img.png" alt=""><figcaption></figcaption></figure>

* 회원 테이블과 주문 테이블을 분리하므로서 이행적 함수 종속 제거

#### (4) BCNF

💡 결정자가 후보키가 아닌 함수 종속 관계를 제거하여 모든 결정자가 후보키인 상태를 말한다.

* 결정자
  * 특정 종속자를 결정짓는 요소, ‘X → Y’ 일때 X는 결정자, Y는 종속자이다.

### &#x20;

### 3. 함수 종속성 (Functional Dependency)

* A값에 의해 B값이 유일하게 정해지는 관계
* B는 A에 함수 종속이다
  * A 결정자(Determinant), B 종속자 (Dependant)

<figure><img src="https://blog.kakaocdn.net/dn/xaZK3/btrQruEBATx/6hVUZrYnexbkEvsULvPmSk/img.png" alt=""><figcaption></figcaption></figure>

* 여러 학생이들이 있고 각 학생별로 여러 과목을 수강한다고 할 때, 위 테이블의 기본키는 (학번, 과목번호)

<figure><img src="https://blog.kakaocdn.net/dn/Ug7na/btrQw18veFg/RJZwY2dvKSxMYD4fAdkLw1/img.png" alt=""><figcaption></figcaption></figure>

#### (1) 완전 함수 종속

💡 기본키를 구성하는 모든 속성에 종속되는 경우

* 성적은 학번, 과목번호를 모두 알아야 유일하게 결정된다 ⇒ 완전 함수 종속

#### (2) 부분 함수 종속

💡 기본키를 구성하는 속성의 일부에 종속되거나, 기본키가 아닌 다른 속성에 종속되는 경우

* 이름과 학년은 학번 하나만 알아도 유일하게 결정된다. ⇒ 부분 함수 종속

#### (3) 이행적 함수 종속

💡 A,B,C 세 속성이 있고 A→B, B→C 종속 관계가 있을 때, A→C가 성립하는 경우

### &#x20;

### 4. 반정규화

💡 데이터베이스의 성능 향상을 위하여, 데이터 중복을 허용하고 조인을 줄이는 데이터베이스 성능 향상 방법

* 조회에 대한 처리 성능이 중요하다고 판단될 때, 부분적으로 반정규화를 고려하게 된다.
* 반정규화는 조회 속도를 향상시키지만, 데이터 모델의 유연성은 낮아진다.

### 결론

* 정규화는 데이터 조회시 많은 Join을 유발하기 때문에 CPU와 메모리을 많이 사용한다. ⇒ 응답시간이 느려진다.
* 반정규화를 적용하다보면 중복된 데이터에 의해 데이터의 일관성과 무결성이 깨질 수 있다.
* 정규화와 반정규화의 적절한 타협점을 찾아야한다.

###

### Reference

[https://rebro.kr/159](https://rebro.kr/159)

[https://rebro.kr/160](https://rebro.kr/160)

[https://www.youtube.com/watch?v=DHyYQod48pk﻿](https://www.youtube.com/watch?v=DHyYQod48pk)
