---
theme: default
title: Переосмысляем систему типов TypeScript
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: none
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
hideInToc: true
fonts:
  sans: Roboto
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
---

# Дырявое решето типов

Разбираемся с отношением подтипов в TypeScript

<!--
Всех приветствую на моём докладе "Дырявое решето типов" или "Разбираемся с отношением подтипов в TypeScript". Почему именно такое название, поймёте в ходе доклада.
-->

---
level: 2
hideInToc: true
---

# Приятно познакомиться

- Василий Алфертьев
- Frontend разработчик (React + TypeScript)
- Компания: Открытые решения
- Telegram: @alfertev2012
- GitHub: alfertev2014

<!--
Сперва, кто я такой. Я Василий Алфертьев, в настоящее время работаю frontend-разработчиком в компании Открытые решения, пишу на React-е, активно использую TypeScript в работе. Ничего особенного, делаю интерфейсы различных одностраничных приложений.
-->

---
layout: two-cols-header
level: 2
hideInToc: true
---

# Чем ещё владею

::left::

- 5+ лет в **С++**:
  - системное программирование в Linux
  - UI на Qt
- ~6 лет в **Java**:
  - backend на Spring
  - базы данных
  - монолиты, микросервисы…

::right::

- Увлекаюсь
  - дизайном языков программирования
  - best practices и архитектурой ПО
  - математической логикой
- **Фанат систем типов**
- Тянет разбираться в
  - компиляторах и оптимизациях
  - "кишках" runtime разных языков
  - IDE и инструментах
  - LISP, Prolog, OCaml, Haskell, Scala…

<!--
Когда-то занимался системным программированием на C++, писал UI на Qt, прошёл через backend-разработку на Java. Параллельно увлекался изучением вопросов дизайна языков программирования, математическими основами, которые за ними стоят... Фанат систем типов. Люблю поинтересоваться, как устроены компиляторы, IDE и другие инструменты.

И вот с этим опытом я пришёл во frontend-разработку, возлагая на TypeScript определённые надежды. Какое-то время я просто наблюдал за его развитием, а потом решил, что всё, пора.
-->
---
layout: default
dragPos:
  tapl: 632,100,300,_
  typing_rules: 107,214,487,_
  complex_type1: 74,206,803,_
  complex_type2: 93,309,800,_
  mortal_combat: 246,223,516,_
  typescript_is_bad: 210,289,198,_
  bad_ts: 431,317,428,_
  prototype_chain: 462,122,381,_
  proxy: 212,342,238,_
  devid: 572,334,271,_
  tsgo: 475,105,464,_
  tsgo_link: 33,370,439,_
---

# Вокруг TypeScript много интересного

<div v-click="[1, 2]">
  <img v-drag="'tapl'" src="./images/tapl.png" />
  <img v-drag="'typing_rules'" src="./images/typing_rules.png" />
</div>
<div v-click="[2, 3]">
  <img v-drag="'complex_type1'" src="./images/complex_type1.png" />
  <img v-drag="'complex_type2'" src="./images/complex_type2.png" />
</div>
<div v-click="[3, 4]">
  <img v-drag="'mortal_combat'" src="./images/mortal_combat.png" />
</div>
<div v-click="[4, 5]">
  <img v-drag="'typescript_is_bad'" src="./images/typescript_is_bad.png" />
  <div v-drag="'bad_ts'">
    <p>Why TypeScript is Bad</p>
    <p><a href="https://t.me/why_typescript_is_bad">https://t.me/why_typescript_is_bad</a></p>
  </div>
</div>
<div v-click="[5, 6]">
  <img v-drag="'prototype_chain'" src="./images/prototype_chain.png" />
  <img v-drag="'proxy'" src="./images/proxy.png" />
  <img v-drag="'devid'" src="./images/devid.png" />
</div>
<div v-click="[6, 7]">
  <img v-drag="'tsgo'" src="./images/tsgo.png" />
  <div v-drag="'tsgo_link'">
    <p><a href="https://github.com/microsoft/typescript-go">https://github.com/microsoft/typescript-go</a></p>
  </div>
</div>

<v-clicks>

- Погружение в теорию типов
- Программирование на типах
- Сравнение статической типизации с динамической
- Разбор *всех* проблем TypeScript
- Метапрограммирование на прототипах и Proxy
- Миграция tsc на Go

</v-clicks>

<!--
Потому что в TypeScript много чего удивительного, несмотря на кажущуюся простоту.

1.
-->

---
layout: default
hideInToc: true
---

# План доклада

1. Проблема строгого языка
1. Коротко про TypeScript и его систему типов
1. Проблемы отношения подтипов в TypeScript на примерах
1. Возможные пути решения
1. Заключение

---
layout: section
---

# Проблема строгого языка

В динамической среде исполнения

---
layout: default
level: 2
dragPos:
  drake: 98,38,439,_
  cwa_owa: 382,334,417,_
  stat_dyn: 370,132,474,30
---

<img v-drag="'drake'" src="./images/drake.png" />
<div v-drag="'stat_dyn'">Статическая/динамическая типизация</div>
<div v-drag="'cwa_owa'">Closed/Open World Assumptions</div>

---
level: 2
layout: image-right
image: ./images/cwa.png
---

# Closed World Assumption

- *Полная* информация о поведении программы
- Нет *непредсказуемых* динамических изменений в среде исполнения
- Можно вести рассуждения об исполнении *статически*

---
level: 2
layout: image-right
image: ./images/owa.png
---

# Open World Assumption

- Динамическая загрузка модулей
- Неявное влияние модулей друг на друга через runtime
- Динамическое применение конфигураций
- eval и интерпретаторы

---
level: 2
title: Проблема
layout: default
---

## Задача

<br/>

- Задействовать возможности TypeScript для ужесточения языка **прикладного кода**, используя типы как **_спецификацию_**.

<v-click>

## Проблема

<br/>

- Система типов TypeScript имеет **существенные недостатки**, не позволяющие обеспечить строгие гарантии.

</v-click>

<!--
Presenter note with **bold**, *italic*, and ~~striked~~ text.

Also, HTML elements are valid:
<div class="flex w-full">
  <span style="flex-grow: 1;">Left content</span>
  <span>Right content</span>
</div>
-->

---
level: 2
layout: default
---

# А так хотелось рассказать...

- Типы как спецификация строгих DSL
- Зависимые типы и вычисления на типах
- Верификация программ
- Оптимизирующие компиляторы с информацией о типах
- Система доказательств теорем на типах

---
level: 2
layout: default
dragPos:
  dreaming_girl: 174,87,655,_
  ocaml1: 30,324,346,_
  ocaml2: 29,258,344,_
---

<img v-drag="'dreaming_girl'" src="./images/dreaming_girl.png" />
<img v-drag="'ocaml1'" src="./images/ocaml1.png" />
<img v-drag="'ocaml2'" src="./images/ocaml2.png" />

---
level: 2
layout: default
---

# DISCLAIMER

- Спикер не призывает к обязательному использованию TypeScript или других способов статической типизации для JavaScript.
- Спикер не ставит целью показать причины, по которым стоит отказаться от TypeScript.
- Спикер понимает, что можно писать надёжно и эффективно и на JavaScript без типов.

---
layout: section
---

# Коротко про TypeScript

И его систему типов

---
layout: section
---

# Плюсы и минусы

---
layout: default
dragPos:
  ts_logo: 640,113,220,_
  microsoft: 648,383,209,_
---

# Коротко про TypeScript

- Язык программирования
- Синтаксис основан на JavaScript
- Транспиляция в JavaScript
- Проверка типов
- Поддержка IDE (language server)

<img v-drag="'ts_logo'" src="./images/ts_logo.png" />
<img v-drag="'microsoft'" src="./images/microsoft.png" />

---

# TypeScript хвалят за

- Предотвращение runtime-ошибок (TypeError)
- Продуктивность разработки (поддержка в IDE)
- Документируемость кода и API
- Прививание хорошего стиля кода
- Избавление от лишних проверок в runtime

---

# TypeScript ругают за

- Ненадёжная система типов
- Слабо типизированная стандартная библиотека JavaScript
- Неактуальные “.d.ts”-файлы для библиотек из NPM
- Высокий порог входа (“трёх-этажные типы”)
- Борьба с ошибками компиляции

---
layout: section
---

# (Не)Надёжность

---
layout: default
---

# Надёжность системы типов TypeScript

[https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)

**Non-goals:**

- Apply a **sound**`*` or "**provably correct**" type system. Instead, strike a balance between correctness and productivity.

`*` Система типов является надёжной (“**sound**”), если статически выведенные типы выражений **_гарантированно_** соответствуют значениям в runtime.

---
layout: default
---

# И как с этим жить?

- Мы **_хотим_**, чтобы типы в коде были верными
- Просто *ответственность за это ложится на разработчика*
- Type checker - просто инструмент
- Для обеспечения гарантий нужны *best practices* и *соглашения*

---
dragPos:
  first_time: 262,29,404,_
  cpp: 442,272,54,_
---

<img v-drag="'first_time'" src="./images/first_time.png" />
<div v-drag="'cpp'" style="background-color: white;text-alignment: center">
<b>C++</b>
</div>

---
layout: default
---

# Ненадёжная система типовc

- Намеренное нарушение надёжности
  - any - неявное приведение к чему угодно
  - as - потенциально ошибочные приведения типов
  - is (type predicates) - потенциально ошибочные
  - declare - по сути то же самое, что и as
- Отключаемая надёжность (флаги strict)
- Взаимодействие с JavaScript-кодом
  - В JavaScript по-умолчанию всё any
  - Слабо типизированная стандартная библиотека
  - “Магия” изменяемых прототипов, Object.defineProperty, Object.freeze, Proxy, переопределение instanceof, оператор delete…
- Врождённые проблемы в дизайне системы типов


---

# Monaco Editor

Slidev provides built-in Monaco Editor support.

Add `{monaco}` to the code block to turn it into an editor:

```ts {monaco}
import { ref } from 'vue'
import { emptyArray } from './external'

const arr = ref(emptyArray(10))
```

Use `{monaco-run}` to create an editor that can execute the code directly in the slide:

```ts {monaco-run}
import { version } from 'vue'
import { emptyArray, sayHello } from './external'

sayHello()
console.log(`vue ${version}`)
console.log(emptyArray<number>(10).reduce(fib => [...fib, fib.at(-1)! + fib.at(-2)!], [1, 1]))
```

---
layout: center
class: text-center
---

# Learn More

[Documentation](https://sli.dev) · [GitHub](https://github.com/slidevjs/slidev) · [Showcases](https://sli.dev/resources/showcases)

<PoweredBySlidev mt-10 />
