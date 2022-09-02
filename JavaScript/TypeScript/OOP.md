# OOP

## 객체지향

객체(Object)는 하나의 역할을 수행하는 변수와 메서드를 그룹핑한 것이다.<br><br>

<b>객체지향 프로그래밍</b>은 서로 관련있는 데이터와 함수를 객체로 정의해서 서로 상호작용할 수 있도록 프로그래밍하는 것이다.<br>
절차지향 프로그래밍은 데이터를 받아와서 처리하는 방법을 사용하고, 객체지향 프로그래밍은 프로그램을 '객체'단위로 나누어 정의하고 각 객체들 간의 상호작용을 통해 프로그래밍을 한다.<br>
레고 부품(=객체)을 조립하여 완성품(=프로그램)을 만드는 것이라고 생각하면 된다.

### 장점

코드 재사용을 통해 생산성을 높이고, 유지 보수에 용이하다.<br>
완성된 코드에서 특정 기능만 교체할 수 있다. -> 확장성

### 단점

개발자 입장에서 코딩의 난이도가 상승하고 개발 속도가 느려진다.<br>
객체지향 프로그래밍은 높은 이해도가 필요하고 설계 단계에서 시간이 많이 소요되기 때문이다.

## 타입스크립트와 OOP

### 캡슐화

서로 연관된 데이터와 함수를 캡슐화, 즉 하나의 객체로 묶어 두는 것을 뜻한다.<br>
예를 들어 사람이라는 객체가 있으면 이 객체는 이름, 나이라는 데이터를 가질 수 있고 먹다, 일하다 라는 함수를 가질 수 있다.<br>

#### 정보 은닉 (Information Hiding)

프로그램의 세부 구현을 외부로 드러나지 않도록 특정 모듈 내부로 감추는 것이다. <br>
서로 연관 있는 데이터와 함수들을 오브젝트 안에 담아두고 외부에서 보일 필요가 없는 세부 데이터를 숨겨 놓음으로써 외부에서 데이터를 변경할 수 없게 한다. <br>
일반적으로 세 종류의 접근 제한이 사용된다.

- constructor

  - class로 instance(object)를 만들 때 항상 호출되는 함수로, 데이터를 담아 호출 할 수 있다.
  - static 키워드를 사용하여 object를 만드는 함수를 제공한다면 private constructor로 설정하여 static method를 사용하도록 권장한다.

- public

  - 클래스의 외부에서 사용 가능하게 노출한다.
  - 외부에서 데이터를 확인 및 변경할 수 있다.
  - class 안에서 변수나 함수에 따로 키워드를 사용하지 않으면 기본값으로 public이 적용된다.

- private

  - 클래스의 내부에서만 사용되며 외부로 노출되지 않는다.
  - 외부에서 데이터를 확인 및 변경할 수 없다.

- protected
  - 다른 클래스에게는 노출되지 않지만, 상속받은 자식 클래스에게는 노출된다.
  - 외부에서 접근할 수 없고, 상속받은 class만 접근할 수 있다.

```ts
{
  class Person {
    name: string;
    age: number;

    constructor(name: string, age: number) {
      this.name = name;
      this.age = age;
    }

    eat(): string {
      return `이름은 ${this.name}. 나이는 ${this.age}. 지금 밥을 먹고있다`;
    }

    work(): string {
      return `이름은 ${this.name}. 나이는 ${this.age}. 지금 일을 하고있다`;
    }
  }

  const person = new Person('yelee', 20);

  console.log(person); // Person { name: 'yelee', age: 20 }
}
```

```ts
class Animal {
  private emotion: string = '';
  private energy: number = 0;

  constructor(
    private animalType: string,
    private name: string,
    private gender: string
  ) {
    this.animalType = animalType;
    this.name = name;
    this.gender = gender;
  }
  play() {
    this.emotion = 'happy';
  }
  eat(food: number) {
    this.energy = food;
  }
}

const dog = new Animal('dog', 'sam', 'male');
const cat = new Animal('cat', 'jang', 'female');
```

### 추상화

접근제어자(private)를 통해 외부에서 객체 내부의 특정 기능을 못보게 함으로써, 외부에서 보이는 인터페이스를 통해 내부 기능을 전부 이해하지 못해도 사용할 수 있다.<br>
예를 들어, 사람 객체 안에 이름, 나이 데이터가 있고 먹다, 일하다, 말하다 함수가 있지만 내부적으로 먹다와 일하다 함수가 어떻게 구현되어있는지 몰라도 말하다 함수 호출 시 먹다와 일하다 함수를 사용할 수 있다.

```ts
{
  interface PersonImpl {
    speak(): string;
  }

  class Person implements PersonImpl {
    private name: string;
    private age: number;

    constructor(name: string, age: number) {
      this.name = name;
      this.age = age;
    }

    private eat(): string {
      return `이름은 ${this.name}. 나이는 ${this.age}. 지금 밥을 먹고있다`;
    }

    private work(): string {
      return `이름은 ${this.name}. 나이는 ${this.age}. 지금 일을 하고있다`;
    }

    speak(): string {
      this.eat();
      this.work();
      return `${this.name} says 안녕!`;
    }
  }

  const person = new Person('yelee', 20);
  console.log(person.speak());
}
```

```ts
class Animal {
  private emotion: string = '';
  private energy: number = 0;

  constructor(
    private animalType: string,
    private name: string,
    private gender: string
  ) {
    this.animalType = animalType;
    this.name = name;
    this.gender = gender;
  }

  private energyUp(energy: number) {
    this.energy = energy;
  }

  private energyDown() {
    this.energy -= -1;
  }

  private happyEmotion() {
    this.energyDown();
    this.emotion = 'happy';
  }

  play() {
    this.happyEmotion();
  }

  eat(food: number) {
    this.energyUp(food);
    this.happyEmotion();
  }
}

const dog = new Animal('dog', 'sam', 'male');

dog.eat();
dog.play();
```

### 상속

기존에 생성된 사람이라는 객체를 상속받아 사람2, 사람3, 사람4, ... 라는 객체를 새롭게 생성할 수 있다. <br>
이 사람이라는 객체를 상속받은 사람2, 사람3, ... 객체는 상속받은 객체(부모 객체)의 함수를 그대로 사용할 수 있다.

#### extends

- extends 키워드를 사용하여 class를 상속받을 수 있다.
- 상속할 때 constructor가 private일 경우 상속되지 않는다. public 또는 자식 클래스에서 접근이 가능하도록 protected 키워드를 사용해야 한다.
- 자식 클래스가 부모 클래스의 함수를 이용하고 싶다면 super 키워드로 접근할 수 있다.
- 자식 클래스에서 constructor는 반드시 super를 호출해야 하며 부모 클래스에서 필요한 데이터는 super를 이용해 전달해야 한다.
- 상속의 단점: 타입스크립트에서 두가지 이상 상속받을 수 없으며 부모 클래스의 변화가 있을 경우 자식 클래스도 영향을 미친다.

```ts
{
  interface PersonImpl {
    speak(): string;
  }

  class Person implements PersonImpl {
    private name: string;
    private age: number;

    constructor(name: string, age: number) {
      this.name = name;
      this.age = age;
    }

    private eat(): string {
      return `이름은 ${this.name}. 나이는 ${this.age}. 지금 밥을 먹고있다`;
    }

    private work(): string {
      return `이름은 ${this.name}. 나이는 ${this.age}. 지금 일을 하고있다`;
    }

    speak(): string {
      this.eat();
      this.work();
      return `${this.name} says 안녕!`;
    }
  }

  class Person2 extends Person {}

  const person = new Person('yelee', 20);
  console.log(person.speak()); // yelee says 안녕!

  const person2 = new Person2('seoyoon', 100);
  console.log(person2.speak()); // seoyoon says 안녕!
}
```

```ts
class Animal {
  private emotion: string = '';
  private energy: number = 0;

  constructor(
    private animalType: string,
    private name: string,
    private gender: string
  ) {
    this.animalType = animalType;
    this.name = name;
    this.gender = gender;
  }

  private energyUp(energy: number) {
    this.energy = energy;
  }

  private energyDown() {
    this.energy -= 1;
  }

  private happyEmotion() {
    this.emotion = 'happy';
  }

  play() {
    this.happyEmotion();
  }

  eat(food: number) {
    this.energyUp(food);
    this.happyEmotion();
  }
}

class Dog extends Animal {
  constructor(animalType, name, gender) {
    super(animalType, name, gender);
  }

  barking() {
    super.energyDown();
    return 'wang wang';
  }
}

const seryDog = new Dog('dog', 'sery', 'female');
seryDog.barking();
```

### 다형성

상속을 통해서 만들어진 자식 클래스들의 종류와 상관없이 부모 클래스에 정의된 함수를 호출할 수 있다.

```ts
class Animal {
  private emotion: string = '';
  private energy: number = 0;

  constructor(
    private animalType: string,
    private name: string,
    private gender: string
  ) {
    this.animalType = animalType;
    this.name = name;
    this.gender = gender;
  }

  private energyUp (energy: number) {
    this.energy = energy;
  }

  private energyDown () {
    this.energy -=- 1;
  }

  private happyEmotion () {
    this.emotion = 'happy';
  }

  play() {
    this.energyDown();
    this.happyEmotion();
  }

  eat(food: number) {
     this.energyUp(food);
     this.happyEmotion();
  }
}


class Dog extends Animal {
  constructor(animalType, name, gender) {
    super(animalType, name, gender)
  }
  ...
  barking() {
    super.energyDown();
    return 'wang wang';
  }
  ...
}

class Cat extends Animal {
  constructor(animalType, name, gender) {
    super(animalType, name, gender)
  }
  ...
  play() {
    super.play();
  }
  ...
}

const seryDog = new Dog('dog', 'sery', 'female');
seryDog.barking();
```

### static

- 변수나 함수에 static 키워드를 사용하면 class level로 지정되고, static이 사용되지 않으면 object level이다.
- class level은 class와 연결되어 있기 때문에 instance를 생성할 때 포함되지 않는다.
- object level은 this를 사용해 접근할 수 있고, class level을 사용하려면 class이름으로 접근해야 한다.

```ts
class Animal {
  private static emotion: string = '';
  private static energy: number = 0;

  constructor(
    private animalType: string,
    private name: string,
    private gender: string
  ) {
    this.animalType = animalType;
    this.name = name;
    this.gender = gender;
  }

  static makeAnimal(animalType: string, name: string, gender: string) {
    return new Animal(animalType, name, gender);
  }

  private energyUp(energy: number) {
    Animal.energy = energy;
  }

  private energyDown() {
    Animal.energy -= -1;
  }

  private happyEmotion() {
    Amimal.emotion = 'happy';
  }

  play() {
    this.happyEmotion();
  }

  eat(food: number) {
    this.energyUp(food);
    this.happyEmotion();
  }
}

const dog = Animal.makeAnimal('dog', 'sery', 'female');
```

### getter 와 setter

- getter와 setter를 사용하면 더 다양한 연산을 할 수 있으며, 전달된 인자가 정확한지 유효성 검사를 할 수 있다.

```ts
class User {
  constructor(private name: string, private age: number) {
    this.name = name;
    this.age = age;
  }

  get userInfo(): string {
    return `name: ${this.name} , age: ${this.age}`;
  }

  get age(): number {
    return this.age;
  }

  set age(num: number) {
    if (num < 0) {
      throw Error('error');
    }
    this.age = num;
  }
}

const user = new User('sara', 30);
console.log(user.userInfo); // name: sara , age: 30
user.age = 20;
console.log(user.age); // 20
```

- getter, setter 미사용

```ts
class Person {
  name: string;
  job: string;
  introduce: string;
  constructor(name: string, job: string) {
    this.name = name;
    this.job = job;
    this.introduce = name + '는' + job;
  }
}

const person = new Person('현호', '개발자');
console.log(person.introduce);
person.job = '선생님';
console.log(person.introduce);
```

개발자를 하다가 직업을 변경했다고 해보자. 마지막 콘솔에는 현호는선생님이 출력되야 하지만 현호는개발자가 출력된다.

- getter 사용

```ts
class Person {
  name: string;
  job: string;
  get introduce(): string {
    return this.name + '는' + this.job;
  }
  constructor(name: string, job: string) {
    this.name = name;
    this.job = job;
  }
}

const person = new Person('현호', '개발자');
console.log(person.introduce);
person.job = '선생님';
console.log(person.introduce);
```

get(getter)을 사용함으로써 introduce에 접근할 때마다 새로운 값을 할당받아 위의 문제점을 해결할 수 있게 되었다.

- setter + private 사용

```ts
{
  class Person {
    private admin: string;
    get newAdmin(): string {
      return this.admin;
    }
    set newAdmin(name: string) {
      if (name.includes('hyun')) {
        this.admin = name;
      }
    }
    constructor(admin: string) {
      this.admin = admin;
    }
  }
  const person = new Person('hyunho');
  person.newAdmin = 'minsu';
  console.log(person);
  person.newAdmin = 'hyunsu';
  console.log(person);
}
```

private으로 외부에서 접근하지 못하게 했는데, set을 이용해서 다시 접근할 수 있게 한다.
의미없는 행위라고 생각할수도 있지만, 현수일때만 admin을 설정하는 로직처럼 특정 로직을 처리하기 위해 setter를 사용할 수 있다.

#### 출처

- https://yelee.tistory.com/18
- https://velog.io/@760kry/TypeScript-OOP-%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5
- https://velog.io/@leehyunho2001/%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EA%B3%BC-TypeScript
