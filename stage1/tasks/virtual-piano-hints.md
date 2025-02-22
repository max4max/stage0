# virtual-piano-hints

[Описание задания](stage1/tasks/virtual-piano.md)

В выполнении задания поможет видео курса JS30 [JavaScript Drum Kit](https://youtu.be/VuN8qwZoego), ссылка на которое приводится в материалах к заданию. В нём автор выполняет очень похожее приложение. [Код приложения](https://github.com/wesbos/JavaScript30/tree/master/01%20-%20JavaScript%20Drum%20Kit)

Предложенное автором решение можно усовершенствовать, например, использовав делегирование. Также вместо свойства `event.keyCode` лучше использовать `event.сode`, возвращающее более осмысленный и понятный результат. 

Ниже о некоторых особенностях выполнения задания.

### Методы поиска элементов
Для поиска элементов чаще всего используются методы `querySelector` и `querySelectorAll`.

**Пример**  
В html-файле контейнер с клавишами имеет класс `piano`, а клавиши в нём классы `piano-key`.  
Находим элемент с классом `piano` и записываем его в переменную `piano`:
```js
const piano = document.querySelector('.piano');
```
Находим коллекцию элементов с классом `piano-key` и записываем её в переменную `pianoКeys`:
```js
const pianoКeys = document.querySelectorAll('.piano-key');
```
**Обратите внимание** 
- все переменные, соответствующие элементам, которые есть в коде страницы, объявляются при помощи ключевого слова `const`
- также `const` используется для объявления переменных, которые мы не планируем менять
- ключевое слово `let` используется для объявления переменных, значения которых будут изменяться. 

### События мыши
Метод `addEventListener` отлавливает событие элемента и выполняет переданную функцию.
 
**Пример 1**  
При клике по элементу `piano` вызываем функцию `playAudio` 
```js
piano.addEventListener('click', playAudio);
```
**Пример 2**  
При клике по элементу `piano` вызываем функцию `playAudio` с параметром `event`:
```js
piano.addEventListener('click', (event) => playAudio(event));
```

### События клавиатуры
События клавиатуры прослушиваем в глобальном объекте `window` - окне браузера

**Пример**  
При нажатии любой клавиши клавиатуры вызываем функцию `playAudio` :
```js
window.addEventListener('keydown', (event) => playAudio(event));
```
Чтобы узнать какая клавиша нажата, используем свойство `event.code`.  
Узнать код клавиши можно на странице [https://keycode.info/](https://keycode.info/)

**Пример**  
Вызываем функцию `playAudio` только если нажата клавиша пробел:
```js
window.addEventListener('keydown', (event) => {
  if(event.code === 'Space') {
    playAudio();
  }
});
``` 

### Проигрывание звука на странице
В html-файл добавим код плеера и кнопку
```html
<audio src="https://zvukipro.com/uploads/files/2017-09/1504526458_zvuki-prirody-penie-solovya.mp3"></audio>
<button>Play audio</button>
```
В js-файле находим элемент `audio`, находим кнопку, создаём функцию `playAudio`, запускаем её при клике по кнопке
```js
const audio = document.querySelector('audio');
const button = document.querySelector('button');

function playAudio() {
  audio.currentTime = 0;
  audio.play();
}
button.addEventListener('click', playAudio)
```
**Обратите внимание**
- названия переменных - существительные, названия функций - глаголы.
- `audio.currentTime = 0;` запускает проигрывание с начала трека

### Рефакторинг функции playAudio
Мы можем не добавлять элемент `audio` в html. Плеер можно создать средствами js
```js
function playAudio(src) {
  const audio = new Audio();
  audio.src = src;
  audio.currentTime = 0;
  audio.play();
}
const button = document.querySelector('button');
const url = 'https://zvukipro.com/uploads/files/2017-09/1504526458_zvuki-prirody-penie-solovya.mp3';
button.addEventListener('click', () => playAudio(url))
``` 
**Обратите внимание**
- предложенный рефакторинг существенно сократил количество кода, но одновременно сократил возможности. Необходим ли он, решаете исходя из своих задач.

### Делегирование
Генерал, руководящий боевыми действиями, отдаёт приказы полкам и дивизиям, а не отдельным солдатам.    
Если у нескольких элементов одинаковый функционал, находим их общего родителя и создаём обработчик событий для него одного.  
**Пример**  
Добавим обработчик событий элементу `piano`, затем проверим, является ли элемент, по которому кликнули, его клавишей (имеет ли он класс `piano-key`) и для этой клавиши запустим функцию `playAudio`.
```js
const piano = document.querySelector('.piano');

piano.addEventListener('click', (event) => {
    if(event.target.classList.contains('piano-key')) {
      playAudio(src);
    }   
  });
```

### data-атрибуты
data-атрибуты позволяют связать данные с элементами на странице. С их помощью мы можем, например, указать какая нота будет проигрываться при клике по клавише виртуального пианино. Для этого в вёрстке для каждой клавиши пианино указываем data-атрибут с произвольным названием и значением, соответствующим названию музыкального файла, который проигрывается при клике по этой клавише.
**Пример**  
```html
<div class="piano-key" data-note="c"></div>
``` 
В js-файле находим элемент `audio`, создаём функцию `playAudio` в которой указываем `audio.src`, указываем, что проигрывать нужно с начала трека, запускаем проигрывание.
```js
piano.addEventListener('click', (event) => {
    if(event.target.classList.contains('piano-key')) {
      const note = event.target.dataset.note;
      const src = `assets/audio/${note}.mp3`;
      playAudio(src);
    }   
  });
```

### Подсвечиваем активную клавишу 
Для изменения стиля элемента используем метод `classList`.  
Метод `classList` является идеальным для работы с классами. Он поддерживается всеми современными браузерами.  
- `element.classList.add('class');` - добавляет элементу класс;
- `element.classList.remove('class');` - удаляет класс;
- `element.classList.toggle('class');` - переключает класс: добавляет, если класса нет, и удаляет, если он есть.
- `element.classList.contains('class');` - проверяет наличие у элемента класса.

Так как метод `classList` работает только с классами, то в кавычках внутри может находиться только название класса. И вот перед этим названием класса точка не ставится **никогда**. Эту ошибку часто совершают начинающие разработчики.

**Пример**
Добавим клавише, по которой кликнули, класс `active`. Свойства для этого класса указаны в css-файле.
```js
piano.addEventListener('click', (event) => {
  if(event.target.classList.contains('piano-key')) {
    pianoКeys.forEach((el) => {
      if(el.classList.contains('active')) {
        el.classList.remove('active');
      }
    });
    event.target.classList.add('active');
  }
});
```
**Обратите внимание**
- используем делегирование - отслеживаем клик не на каждой клавише, а на их общем родителе
- `event.target`- элемент, на котором произошло событие. Проверяем, что кликнули по клавише
- pianoКeys - все клавиши пианино. Пробегаемся по ним и удаляем класс `active`, если он есть
- затем клавише, на которой произошло событие, добавляем класс `active`

### Переключение Notes/Letters
Для отображения возле клавиш пианино названий нот или букв клавиатуры использовались псевдоэлементы `::after` и `::before`. Это удобно с точки зрения вёрстки, но создаёт проблемы для управления их состоянием: напрямую повлиять на псевдоэлементы средствами JavaScript невозможно.

Псевдоэлементы `::after` и `::before` не находятся при помощи JavaScript, им нельзя указать дополнительные свойства, например, `display: none` или `display: block`, чтобы скрыть или отобразить. Но можно найти их общего родителя, указать ему дополнительный класс и уже для этого класса через `css` прописать нужные свойства псевдоэлементам. 

### Как можно усовершенствовать приложение
Если предложенное задание показалось вам слишком простым и элементарным, присмотритесь к Web Audio API, которое позволит вам генерировать ноты, а не скачивать их.
- [Introduction to Web Audio API](https://css-tricks.com/introduction-web-audio-api/)
- [Пример кода и демо](https://codepen.io/kbrammer/pen/Wdjday)