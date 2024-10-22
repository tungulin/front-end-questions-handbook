## Разбор принципов ООП (с примерами на Typescript):

### 1. Три главных кита на которых строится ООП.

1. Инкапсуляция
2. Наследование
3. Полиморфизм

#### 1.1. Инкапсуляция

Предназначена для изоляции определенных методов и переменных класса. Проще говоря, мы скрываем переменные и методы, которые не нужно показывать наружу.

Пример 1:

```ts
class Person {
  public name;
  public age;
  private hobby;

  constructor(name: string, age: number, hobby: string) {
    this.name = name;
    this.age = age;
    this.hobby = hobby;
  }

  public getBio() {
    return `Name: ${this.name}. Age: ${this.age}`;
  }

  private getHobby() {
    return `Hobby: ${this.hobby}`;
  }
}

const Person1 = new Person("Andrey", 22, "basketball");

console.log("Variable name:", Person1.name);
console.log("Variable hobby:", Person1.hobby); // Error: Property 'hobby' is private and only accessible within class 'Person'
console.log("Method getBio:", Person1.getBio()); // Name: Andrey. Age: 22
console.log("Method getBio:", Person1.getHobby());
```

В примере представлено получение данных пользователя. public переменные/методы - мы можем получить. private переменные/методы - выдают ошибку.

#### 1.2. Инкапсуляция

Классы могут наследовать друг друга. При этом один класс (дочерний), который наследуется от другого (родительский). Получает все методы и переменные этого класса.

При этом получается только методы с видимостью public, protected. Protected переменные/методы видны только дочерним классам.

<!-- todo: добавить пример -->

#### 1.3. Полиморфизм

Говоря просто - полиморфизм способность метода возвращать разные значения, согласно определённым условиям.

```ts
class Animal {
  public name: string;

  constructor(name: string) {
    this.name = name;
  }

  public run(): void {
    console.log("Животное бежит!");
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name);
  }

  public run(): void {
    console.log("Собака бежит быстро!");
  }
}

class Cat extends Animal {
  constructor(name: string) {
    super(name);
  }

  public run(): void {
    console.log("Кошка бежит медленно!");
  }
}
```

Через общий интерфейс класс Animal - класс Dog и Cat наследуясь от него получают метод run() и у каждого класса своя реализация этого метода. При этом происходит переопределение этого метода.

### 2. Принцип SOLID.

#### 2.1. S - Single Responsibility (Принцип единственной ответственности)

Класс должке быть ответственен только за что-то одно. К примеру:

```ts
class Person {
  public name: string;

  constructor(name: string) {
    this.name = name;
  }

  public getName(): void {
    return `Name: ${this.name}`;
  }

  public savePersonToDB(): {
    // db operation
  };
}
```

В данном случаи принцип единной ответствеености нарушается - так как класс Person должен по логике манипулировать свойствами объекта. Тут лучше выделить второй класс PersonDB

#### 2.2. O - Open-Closed (Принцип открытости-закрытости)

Классы должны быть открыты для расширения, но закрыты для модификации. Это сделано чтобы не нарушать уже существующию логику работы класса и классов, которые наследуются.

#### 2.3. L - Liskov Substitution (Принцип подстановки Барбары Лисков)

Методы класса должны быть заменяемы методами его производных (наследумых) классов без изменения корректности программы. Попроще, если класс не переопределяет методы наследумого класса, то те методы родительского класса должны работать корректно.

<!-- todo: add -->

#### 2.4. I - Interface Segregation (Принцип разделения интерфейсов)

Нужно создавать узконаправленные интерфейсы. Для того, чтобы не было лишних методов у классов, который этот интерфейс используют

```ts
interface Shape {
  calculateRadius: () => number;
  countSquare: () => number;
}

class Circle implements Shape {
  calculateRadius() {
    //calculation
    return 10;
  }

  countSquare() {
    //counting
    return 35;
  }
}

class Box implements Shape {
  calculateRadius() {
    //calculation
    return 0;
  }

  countSquare() {
    //counting
    return 19;
  }
}
```

Интерфейс Shape реализует общие методы для классов Box и Circle, но в нем есть ненужные метод для Box - calculateRadius. Тут как раз таки и нарушается принцип разделения интерфейсов.

В данном случаи правильно сделать так.

Пример:

```ts
interface Shape {
  countAngles: () => number;
}

interface IBox extends Shape {}

interface ICircle extends Shape {
  calculateRadius: () => number;
}
```

#### 2.5. D - Dependency Inversion (Принцип инверсии зависимостей)

Принцип инверсии зависимостей означает, что высокоуровневые модули не должны зависеть от низкоуровневых модулей. Оба должны зависеть от абстракций. Это означает, что зависимости внутри приложения должны быть направлены на абстракции (например, интерфейсы), а не на конкретные реализации.

Пример:

```ts
class EmailService {
  sendEmail(message) {
    console.log(`Отправка email с сообщением: ${message}`);
  }
}

class Notification {
  constructor() {
    this.emailService = new EmailService();
  }

  sendNotification(message) {
    this.emailService.sendEmail(message);
  }
}

const notification = new Notification();
notification.sendNotification("Привет!");
```

В данном примере класс Notification напрямую зависит от класса EmailService. Если нам нужно будет изменить способ отправки уведомлений с почты на SMS - нам придется менять класс Notification. Чтобы такого избежать - будем делать зависимость от абстракции.

```ts
// Абстракция для уведомлений
class NotificationService {
  send(message) {
    console.log("Send:", message);
  }
}

// Реализация для отправки email
class EmailService extends NotificationService {
  send(message) {
    console.log(`Отправка email с сообщением: ${message}`);
  }
}

// Реализация для отправки SMS
class SmsService extends NotificationService {
  send(message) {
    console.log(`Отправка SMS с сообщением: ${message}`);
  }
}

class Notification {
  constructor(notificationService) {
    this.notificationService = notificationService;
  }

  sendNotification(message) {
    this.notificationService.send(message);
  }
}

// Использование
const emailService = new EmailService();
const smsService = new SmsService();

const emailNotification = new Notification(emailService);
const smsNotification = new Notification(smsService);

emailNotification.sendNotification("Привет через email!");
smsNotification.sendNotification("Привет через SMS!");
```
