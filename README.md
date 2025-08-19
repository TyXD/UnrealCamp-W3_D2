# [내일배움캠프 언리얼5기] 동적할당, 객체지향 프로그래밍

# 1. 오늘 학습 키워드

>## 동적 할당

>## 객체지향 프로그래밍

## 2. 오늘 학습 한 내용을 나만의 언어로 정리하기

## 동적 할당 

## 1. CPU와 메모리

CPU (Central Processing Unit): 프로그램의 명령어를 해석하고 연산(계산)을 수행하는 두뇌 역할을 합니다.

메모리: 명령어와 데이터를 저장하는 공간입니다. 여러 메모리 계층이 존재합니다.

# 2. 메모리 계층

단순히 "메모리"라고 하면 보통 RAM(Random Access Memory)을 떠올리지만, 

컴퓨터에는 속도와 용도에 따라 여러 종류의 메모리가 계층적으로 존재합니다.


레지스터: CPU 내부에 존재하는 가장 빠르고 작은 메모리입니다. 

CPU는 버스를 거치지 않고 직접 접근할 수 있어 속도가 매우 빠릅니다.


캐시: CPU와 RAM 사이에 위치하여 속도 차이를 완충하는 역할을 합니다. 

CPU가 자주 사용할 것으로 예상되는 데이터를 RAM에서 미리 가져와 저장해 둡니다.


RAM: 프로그램이 실행될 때 명령어와 데이터가 올라가는 주 메모리 공간입니다. 레지스터나 캐시에 비해 용량이 크지만, 

CPU가 데이터에 접근하려면 시스템 버스를 거쳐야 하므로 상대적으로 느립니다.


보조 기억 장치: SSD, HDD 등 전원이 꺼져도 데이터가 보존되는 비휘발성 저장 공간입니다. 속도는 가장 느리지만 용량이 매우 큽니다.


## 3. register 키워드의 의미와 현재

과거에는 C언어 초기에는 매모리가 부족하고 컴파일러의 최적화 기술이 부족했습니다. 이때 프로그래머가 register 키워드를 사용해 

i와 같이 반복문에서 빈번하게 사용되는 변수를 "RAM이 아닌 CPU 레지스터에 저장"이라고 컴파일러에게 요청했습니다. 

레지스터에 변수를 저장하면 접근 속도가 비약적으로 빨라져서 프로그램 성능이 향상되었습니다.

현대의 컴파일러는 매우 지능적이어서 코드의 문맥을 분석해 어떤 변수를 레지스터에 저장하는 것이 효율적인지 스스로 판단합니다. 

따라서 현재 register 키워드는 효율적이지 않다면, 컴파일러가 무시합니다. 

## 4. register 변수의 제약사항

레지스터는 메모리의 일부가 아니므로 주소를 가질 수 없습니다. 

따라서 register로 선언된 변수는 주소 연산자(&)를 사용할 수 없으며, 포인터로 가리킬 수 없습니다.

## 5. 프로그램의 메모리 구조

프로그램이 실행되면 운영체제는 다음과 같은 논리적인 메모리 공간을 할당합니다.

코드 섹션: 작성한 소스 코드가 저장되는 영역입니다.

데이터섹션: 전역 변수와 정적 변수가 저장됩니다. 

힙(Heap) 섹션: 프로그래머가 원할때 메모리를 동적으로 할당하고 반납하는 공간입니다.

스택(Stack) 섹션: 함수 호출 시 사용되는 지역 변수, 매개변수, 그리고 함수 반환 주소 등이 저장됩니다.

함수가 호출될 때 생성되고 함수가 종료되면 자동으로 소멸됩니다.

## 6. 스택과 힙 언제 무엇을 써야 할까?'

>### 스택 메모리

장점으로 메모리 할당(O(1)), 해제(즉시)가 매우 빠릅니다.

단점으론 컴파일 시에 크기가 결정되어야 하며, 더 필요해도 추가할 수 없습니다.

또한 함수가 종료되면 데이터가 사라집니다. 

>### 힙 메모리

장점은 런타임 중에 필요한 만큼의 메모리를 동적으로 할당할 수 있고, 

프로그래머가 원하는 시점까지 데이터를 유지할 수 있습니다.

단점은 할당/해제 속도가 스택보다 느리며, 

C에서는 프로그래머가 직접 malloc/free 등을 통해 관리해야 하므로 

메모리 누수(Memory Leak)와 같은 실수가 발생하기 쉽습니다.

그렇기에 가능하다면 스택 메모리를 우선적으로 사용하고, 

런타임에 크기가 결정되거나 함수의 데이터를 유지해야 할 때 힙 메모리를 사용해야 합니다.

C언어와 같은 **언매니지드 언어(Unmanaged Language)**는 프로그래머에게 직접적인 메모리 제어 권한을 줍니다.

이는 강력한 장점이지만, 그만큼 사용할때 제대로 관리를 안하면 큰 책임이 따릅니다.

## 7. 동적 할당과 메모리 소유권

1. 동적 할당 3단계

할당 (Allocation): malloc 함수를 사용해 힙 영역에 필요한 크기의 메모리 공간을 요청하고, 시작 주소를 포인터에 반환합니다.

사용 (Use): 할당받은 메모리 공간을 포인터를 통해 접근합니다. 배열처럼 사용하거나, 포인터 연산을 통해 접근할 수 있습니다.

해제 (Deallocation): free 함수를 사용해 다 쓴 메모리 공간을 시스템에 반환합니다.

이를 통해 다른 프로그램이나 현재 프로그램의 다른 부분이 해당 공간을 재사용할 수 있게 됩니다.

2. 동적 메모리 관련 함수

void* malloc(size_t size)

size 바이트만큼의 메모리 블록을 힙에 할당합니다.

초기화되지 않은 쓰레기 값을 포함하고 있습니다.

할당에 성공하면 해당 메모리의 시작 주소를 void* 타입으로 반환하고, 실패하면 NULL을 반환합니다.

void free(void* ptr)

malloc, calloc, realloc으로 할당받은 메모리 공간을 해제합니다.

이때 할당된 메모리는 반드시 단 한 번만 해제해야 합니다.

그렇기에 malloc을 작성하는 순간, 코드의 맨 아래에 free를 먼저 작성하는 습관을 들이면 메모리 누수를 방지하는 데 큰 도움이 됩니다.

또한, free를 호출한 후에는 해당 포인터에 NULL을 대입하여 댕글링 포인터 문제를 예방하는 것이 좋습니다.

3. 메모리 소유권 (Memory Ownership)

"메모리의 주인은 누구인가?" 라는 질문은 매우 중요합니다. 메모리를 할당한 주체(함수 또는 코드 블록)가 해당 메모리를 해제할 책임을 집니다.

함수 내부에서 동적 할당한 메모리를 반환하는 경우, 이 메모리의 소유권이 함수를 호출한 호출자에게 이전됩니다.

호출자는 이 메모리를 사용한 뒤 반드시 해제해주어야 합니다.

이러한 규칙은 함수명이나 주석을 통해 명확하게 전달되어야 합니다.

## 8. C++ 스타일 동적 할당: new와 delete

C++에서는 C언어의 malloc/free가 가진 문제점들을 개선하고, 

객체지향 패러다임에 더 적합한 새로운 동적 할당 방식을 도입했습니다. 바로 new와 delete 연산자입니다.

## 9. 왜 malloc/free 대신 new/delete를 사용하는가?

malloc은 void*를 반환하므로 프로그래머가 직접 올바른 타입으로 형 변환을 해야 합니다. 

이 과정에서 실수가 발생할 수 있습니다. 반면 new는 할당하려는 데이터 타입의 포인터를 반환하므로 형 변환이 필요 없어 더 안전합니다.

또한 new/delete는 실행할때 클래스의 생성자 및 소멸자를 호출합니다.

new는 메모리를 할당한 후 해당 객체의 생성자를 자동으로 호출합니다. 이때 객체를 초기 상태로 초기화합니다.

delete는 객체의 소멸자를 자동으로 호출한 후 메모리를 해제합니다. 

객체가 소멸되기 전에 소멀자에 등록된 작업을 수행할 수 있도록 보장합니다.

malloc과 free는 단순히 메모리만 할당/해제할 뿐, 생성자와 소멸자를 호출하지 않습니다. 

그렇기에 C++ 클래스에 malloc/free를 사용하는 것은 매우 위험합니다.

## 객체지향 프로그래밍

C언어가 절차 중심으로 문제를 해결하는 절차 지향 프로그래밍이라면,

C++은 데이터와 그 데이터를 처리하는 함수를 하나의 '객체(Object)'로 묶어 문제를 해결하는 객체지향 프로그래밍을 지원합니다.

## 1. 상속

```cpp
// 기본 클래스: Vehicle
class Vehicle {
protected: // protected: 파생 클래스에서는 접근 가능, 외부에서는 접근 불가
    string color;
    int speed;
public:
    // 기본 클래스 생성자
    Vehicle(string c, int s) : color(c), speed(s) {}

    void move() {
        cout << "The vehicle is moving at " << speed << " km/h." << endl;
    }
};

// 파생 클래스: Bicycle
class Bicycle : public Vehicle { // 'public' 상속
private:
    bool hasBasket;
public:
    // 파생 클래스 생성자는 멤버 초기화 리스트를 통해
    // 기본 클래스 생성자를 호출하여 부모 부분을 초기화한다.
    Bicycle(string c, int s, bool basket) : Vehicle(c, s), hasBasket(basket) {}

    void ringBell() {
        cout << "Bicycle bell: Ring Ring!" << endl;
    }
};
```

상속은 기존 클래스(부모 클래스)의 속성과 기능을 새로운 클래스(자식 클래스)가 물려받아 재사용하고 확장하는 개념입니다.

이는 비슷한 코드의 중복을 줄이고 유지보수성을 높이면서 유저가 필요한 정보에만 접근하게 도와줍니다.

예시 코드에서는 기본 클래스, 즉 부모 클래스는 상속의 대상이 되는 클래스인 Vehicle 클래스 입니다.

vehicle 클래스의 파생 클래스, 즉 자식 클래스는 기본 클래스를 상속받아 만들어진 새로운 클래스인 Bicycle, Truck입니다.

## 2. 다형성

다형성이란, 기본이 되는 클래스를 만들어 함수의 인터페이스를 정의하고

실제 구현은 파생 클래스에서 담당하게 하는 기법을 말합니다.

다형성을 어떻게 사용하는지 예시를 통해 설명하겠습니다.

```cpp
#include <iostream>
#include <string>

using namespace std;

class Lion
{
public:
	Lion(string word):m_word(word){}
	void bark() { cout << "Lion" << " " << m_word << endl; }
private:
	string m_word;
};

class Wolf
{
public:
	Wolf(string word) :m_word(word) {}
	void bark() { cout << "Wolf" << " " << m_word << endl; }

private:
	string m_word;
};

class Dog
{
public:
	Dog(string word) :m_word(word) {}
	void bark() { cout << "Dog" << " " << m_word << endl; }

private:
	string m_word;
};

void print(Lion lion)
{
	lion.bark();
}

void print(Wolf wolf)
{
	wolf.bark();
}

void print(Dog dog)
{
	dog.bark();
}


int main()
{
	Lion lion("ahaaaaaa!");
	Wolf wolf("ohhhhh");
	Dog dog("oooooooooooooops");

	print(lion);
	print(wolf);
	print(dog);

	return 0;
}
```
예시에서 Dog 클래스와 같이 새로운 동물 클래스를 추가될 때마다 print(animal animal) 함수를 새로 만들어야 합니다. 이는 확장성이 매우 떨어지는 구조입니다.

그렇지만 다형성을 이용하면 기본 클래스(Animal)의 포인터 또는 참조를 통해 파생 클래스(Lion, Dog 등)의 객체를 다룰 수 있습니다. 

```cpp
#include <iostream>
#include <string>

using namespace std;

class Animal
{
public:
	Animal() {}
	virtual void bark() {};
};

class Lion : public Animal
{
public:
	Lion(string word) :m_word(word) {}
	void bark() { cout << "Lion" << " " << m_word << endl; }
private:
	string m_word;
};

class Wolf : public Animal
{
public:
	Wolf(string word) :m_word(word) {}
	void bark() { cout << "Wolf" << " " << m_word << endl; }

private:
	string m_word;
};

class Dog : public Animal
{
public:
	Dog(string word) :m_word(word) {}
	void bark() { cout << "Dog" << " " << m_word << endl; }

private:
	string m_word;
};

void print(Animal* animal)
{
	animal->bark();
}

int main()
{
	Lion lion("ahaaaaaa!");
	Wolf wolf("ohhhhh");
	Dog dog("oooooooooooooops");

	print(&lion);
	print(&wolf);
	print(&dog);

	return 0;
}
```

이때, print 함수는 Animal* 타입의 매개변수 하나만 받으면 되므로, 새로운 동물이 추가되어도 print 함수를 수정할 필요가 없습니다.

## 3. 가상 함수와 추상 클래스

1. 가상 함수 (Virtual Function)

다형성이 런타임에 올바르게 동작하게 하는 핵심 메커니즘입니다.

기본 클래스의 함수 앞에 virtual 키워드를 붙이면, 

컴파일러는 이 함수가 파생 클래스에서 재정의될 수 있음을 인지합니다.

동적 바인딩은 virtual 함수가 호출될 때, 포인터의 타입(Animal*)이 아닌, 

포인터가 실제로 가리키는 객체의 타입(Lion, Dog)에 따라 호출될 함수가 런타임에 결정됩니다.

```cpp
class Animal {
public:
    // 파생 클래스가 이 함수를 재정의할 것이라고 알림
    virtual void makeSound() {
        cout << "Animal makes a sound." << endl;
    }
};

class Dog : public Animal {
public:
    // 부모의 virtual 함수를 재정의(Override)
    void makeSound() override { // 'override' 키워드는 실수를 방지해주는 좋은 습관
        cout << "Dog barks: Woof! Woof!" << endl;
    }
};
```
2. 순수 가상 함수와 추상 클래스 (Pure Virtual Function & Abstract Class)

Animal이라는 개념 자체의 소리(makeSound)는 정의하기 애매합니다.

모든 동물은 소리를 내지만 모든 동물을 아우르는 추상적인 개념의 소리는 없습니다. 

이런식으로 순수 가상 함수는 기본 클래스에서 구현할 내용이 마땅치 않은 경우에 사용합니다.

순수 가상 함수는 기존 가상함수 = 0을 붙여 선언하며, 파생 클래스는 이 함수를 반드시 재정의해야 한다는 규칙을 강제합니다.

```cpp
virtual void makeSound() = 0; // 순수 가상 함수
```
추상 클래스 (Abstract Class): 순수 가상 함수를 하나 이상 포함하는 클래스입니다.

추상 클래스는 인스턴스, 즉 객체를 직접 생성할 수 없습니다. (Animal myAnimal; -> 컴파일 에러)

오직 파생 클래스의 객체를 가리키는 포인터나 참조 타입으로만 사용될 수 있습니다.

```cpp
#include <iostream>
using namespace std;

// 기본 클래스: Animal
class Animal {
public:
    // 가상 함수: 자식 클래스에서 재정의 가능
    virtual void makeSound() = 0;
};

// 파생 클래스: Dog
class Dog : public Animal {
public:
    void makeSound() {
        cout << "Dog barks: Woof! Woof!" << endl;
    }
};

// 파생 클래스: Cat
class Cat : public Animal {
public:
    void makeSound() {
        cout << "Cat meows: Meow! Meow!" << endl;
    }
};

int main() {
    // Animal 타입 포인터로 다양한 객체를 가리킴
    Animal* myAnimal;
    Dog myDog;
    Cat myCat;

    // Dog 객체 가리키기
    myAnimal = &myDog;
    myAnimal->makeSound();  // Dog의 makeSound() 호출

    // Cat 객체 가리키기
    myAnimal = &myCat;
    myAnimal->makeSound();  // Cat의 makeSound() 호출

    return 0;
}
```

## 3. 내일 학습 할 것은 무엇인지

내일은 과제를 해결하며 객체지향적 프로그래밍에 대해 공부할 예정입니다.

#내일배움캠프 #사전캠프 #TIL 
 
