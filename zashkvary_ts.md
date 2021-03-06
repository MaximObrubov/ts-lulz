#Зашквары ts
---
Ниже приведенные примеры написаны для версии TS 4.2.3 и полны ~ненависти~ сострадания

## Котопес!
```ts
interface Cat {
  legs: number;
  tail: string;
  wiskies: number;
}

class Kitty implements Cat {
  legs = 4;
  wiskies = 8;
  tail = 'Is my document'
}

interface Dog {
  legs: number;
  woof?: () => void;
}

function test(animal: Dog) {
  console.log(animal);
}


// NOTE: передаем кота, как пса иии.... ничего не происходит, TS считает, что всё норм
const fluffy: Cat = { legs: 5, wiskies: 16, tail: 'Not the right way to eat butterbread'};
const ginger = new Kitty();
test(fluffy);
// NOTE: создали кота по другому ииии... ничего не изменилось, TS считает, что всё норм
test(ginger);
```

Более жизненный и более опасный пример:
```ts
class Sphere {
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  volume() {
    return 3 / 4 * Math.PI * Math.pow(this.radius, 3);
  }
}

class Cylinder {
  radius: number;

  height: number;

  constructor(radius: number, height: number) {
    this.radius = radius;
    this.height = height;
  }

  volume() {
    return 2 * Math.PI * Math.pow(this.radius, 2) * this.height;
  }
}

function testFigure(s: Sphere) {
  console.log(s.volume());
}

const s = new Sphere(4);
const c = new Cylinder(4, 2000);

testFigure(c);
```
Вы хочите типов - их нет у меня.
Типы не типы, а структуры, проверка типов - не проверка типов, а проверка совпадения
обязательных полей объектов.

Для нормальной типизации люди извращаются и делают обязательное поле в виде уникального хэша -
и это ЗАШКВАР.

## Перегрузка методов
есть удобная перегрузка функций, почему же, черт возьми, нельзя было сделать перегрузку методов.
https://dev.to/tomekbuszewski/method-overloading-in-typescript-25pm

## Автоматически расширяемые интерфейсы
```ts
interface Stuff {
  prop1: number;
}

interface Stuff {
  prop2: number;
}

const stuff: Stuff = {prop1: 5} // Error: нет свойства `prop2`
```
соснешь, когда происходит импорт одноименного интерфейса, и когда отдаешь свой код наружу

## Амбивалентно-строгие проверки
```ts
interface Staff {
  name: string,
  dataType: any,
}

const obj = {name: 'name', dataType: 3, otherData: 5};
const check: Stuff = obj; // ts считает, что всё ок
const check2: Stuff = {name: 'name', dataType: 3, otherData: 5}; // Error: делаем тоже самое, но происходит ошибка                                                           // `otherData` отсутствиет в типе Stuff
```

## Readonly защищает от изменений!! ура, но только прямые свойства объекта
Узнав про `readonly` модификатор в TS по началу так обрадовался... но только по началу :harold:  как и всё в TS, тут не без особенностей, неизменяемость фиксируется только для прямых ключей объекта, но вложенные объекты/массивы могут меняться как угодно, если вам нужно ДЕЙСТВИТЕЛЬНЫЙ `readonly`, то лучше использовать DeepReadonly из библиотеки ts-essentials, или пользоваться сигнатурой индекса, где вложенность явно фиксирована.

## У вас слишком узкие объекты! (с) TS
Если типы не заданы явно и переменные заданы через `let`'ы, то TS будет автоматически выбирать более широкий тип, если писать `const`, то будет автоматически выбираться более узкий тип.
```
let x = 'x'; // тип string
const x = 'x'; // тип 'x'
let x = 'x' as const; // тип 'x'
```
в объектах по умолчанию система типов работает, как с let, но если прописать явно в объекте `as const` то тип свойства будет сужаться
```
const A = {
   a: 'x' as const // A.a имеет тип 'x'
}
```
Возможно Вас, как и меня смутит то, что `as` употребляется как утверждение типа, и да, оно так применяется... но это как бы другой `as` (:
`as const` - хороший
`as SuperType` - плохой
