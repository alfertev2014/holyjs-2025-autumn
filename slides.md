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
Приветствие и вступление:

Рад всех приветствовать на моём докладе "Дырявое решето типов" или "Разбираемся с отношением подтипов в TypeScript". Название выбрано не просто так, в ходе доклада поймёте, почему.
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
Итак, я Василий Алфертьев, в настоящее время работаю в компании Открытые решения frontend-разработчиком на React-е, активно использую TypeScript в работе.
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
Когда-то занимался системным программированием на C++, писал UI на Qt, прошёл через backend-разработку на Java. Параллельно увлекался изучением вопросов дизайна языков программирования, математическими основами, которые за ними стоят... Фанат систем типов. Люблю поинтересоваться, как устроены компиляторы, работа с синтаксическими деревьями в IDE и других инструментах.

И вот с этим опытом я пришёл во frontend-разработку. Какое-то время я просто наблюдал за развитием TypeScript, а потом решил, что всё, пора, возлагая на него определённые надежды.
-->

---
layout: default
hideInToc: true
---

# План доклада

<Toc text-sm minDepth="1" maxDepth="1" />

---
layout: default
dragPos:
  tapl: 632,100,300,_
  typing_rules: 107,214,487,_
  complex_type1: 74,206,803,_
  complex_type2: 93,309,800,_
  mortal_combat: 251,242,450,_
  typescript_is_bad: 210,289,198,_
  bad_ts: 431,317,428,_
  prototype_chain: 92,123,646,_
  proxy: 153,322,322,_
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
А в TypeScript много чего удивительного, несмотря на его кажущуюся простоту.

[click] This will be highlighted after the first click

[click] Highlighted with `count = ref(0)`

[click:3] Last click (skip two clicks)
-->

---
layout: section
---

# Желание надёжного языка

---
level: 2
layout: image-right
image: ./images/cwa.png
---

# Closed World Assumption

- Полная информация о поведении программы
- Нет непредсказуемых динамических изменений в среде исполнения

---
level: 2
layout: image-right
image: ./images/owa.png
---

# Open World Assumption

- Динамическая загрузка модулей
- Динамическое применение конфигураций
- eval и интерпретаторы
- Неявное влияние модулей друг на друга через runtime

---
level: 2
title: Проблема
layout: statement
---

**Задача**:

- Задействовать возможности TypeScript для ужесточения языка, используя типы как **_спецификацию_**.

**Проблема**:

- Система типов TypeScript имеет **существенные недостатки**, не позволяющие обеспечить строгие гарантии.

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
layout: statement
---

# DISCLAIMER


Спикер не призывает к обязательному использованию TypeScript или других способов статической типизации для JavaScript.

Спикер не ставит целью показать причины, по которым стоит отказаться от TypeScript.

Спикер понимает, что можно писать надёжно и эффективно и на JavaScript без типов.

---
layout: section
---

# Коротко про TypeScript


---

# Коротко про TypeScript

- Язык программирования
- Синтаксис основан на JavaScript
- Проверка типов
- Транспиляция в JavaScript
- Поддержка IDE (language server)


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
foo: bar
dragPos:
  square: 258,376,167,_
---

# Draggable Elements

Double-click on the draggable elements to edit their positions.

<br>

###### Directive Usage

```md
<img v-drag="'square'" src="https://sli.dev/logo.png">
```

<br>

###### Component Usage

```md
<v-drag text-3xl>
  <div class="i-carbon:arrow-up" />
  Use the `v-drag` component to have a draggable container!
</v-drag>
```

<v-drag pos="657,288,261,_,22">
  <div text-center text-3xl border border-main rounded>
    Double-click me!
  </div>
</v-drag>

<img v-drag="'square'" src="https://sli.dev/logo.png">

###### Draggable Arrow

```md
<v-drag-arrow two-way />
```

<v-drag-arrow pos="533,378,236,102" two-way op70 />

---
src: ./pages/imported-slides.md
hide: false
---

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
