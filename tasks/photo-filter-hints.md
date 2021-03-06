## photo-filter-hints

[Описание задания](tasks/photo-filter.md)

В выполнении задания поможет видео курса JS30 [CSS Variables](https://youtu.be/AHLNzv13c2I), ссылка на которое приводится в материалах к заданию. В нём автор выполняет приложение, реализующее часть функциональности предлагаемого проекта. [Код проекта](https://github.com/wesbos/JavaScript30/tree/master/03%20-%20CSS%20Variables).

Предложенное автором решение можно усовершенствовать применив делегирование и отслеживая только одно событие `input` вместо двух событий `change` и `mousemove`, которые отслеживает автор.

Ниже о некоторых особенностях выполнения задания.

### input type="range"
Элемент `<input>` имеет больше 20 разных типов. Каждый из них предназначен для решения определённой задачи. В предложенном задании будем использовать `input type="range"` - ползунок, при помощи которого удобно менять значение.

В html-коде приводится пример такого элемента:

```html
  <input type="range" name="sepia" min="0" max="100" value="0" data-sizing="%">
```

Здесь указаны минимальное, максимальное и текущее значение, а также использован data-атрибут, который указывает на размерность определяемых в `<input>` значений, он нам позже пригодится при написании js-кода.

Получить текущее значение `input type="range"` в js достаточно просто - это `input.value`.  Узнать, что значение изменилось, тоже не сложно, при помощи метода `addEventListener` отслеживаем событие `input` (да, немного запутанно называть одним и тем же словом и html-элемент и js-событие. ну как есть).

О чём вам желательно вспомнить - о делегировании, то есть не навешивать слушатель на каждый ползунок, а отслеживать событие на их общем родителе.

Так как в предложенной разметке у `<input>` нет классов (плохо, знаю, но к такому тоже нужно быть готовыми), метод `element.matches('css')` позволит определить соответствует ли элемент, на котором произошло событие, указанному css-селектору.

### CSS Filters 
Про фильтры читаем [здесь](https://css-tricks.com/almanac/properties/f/filter/)
и [здесь](https://orangeable.com/css/filters) (эти же ссылки есть в материалах к заданию. можно просто посмотреть картинки). Тем, кто предпочитает статьи на русском, про фильтры можно почитать [здесь](https://webformyself.com/filtry-v-css-razmytie-ottenki-serogo-yarkost-i-mnogo-drugix-effektov-v-css/)

Если коротко, фильтры - это красиво. В разработке они могут применяться для добавления различных эффекта, мы их используем для создания редактора фото.

### CSS-переменные
Статьи о css-переменных есть в материалах к заданию. Они удобные, и научиться их использовать полезно. Советую потратить какое-то время и попытаться с ними разобраться, в дальнейшем это позволит ускорить разработку, избежать дублирования стилей. Одно из преимуществ css-переменных - их удобно менять через JavaScript. Благодаря им в нашем приложении вместо отдельной функции для каждого css-фильтра можем написать одну универсальную функцию для смены значения любого фильтра. Как она выглядит показано в видео курса JS30 [CSS Variables](https://youtu.be/AHLNzv13c2I). Единственное, нужно будет немного видоизменить функцию в связи с делегированием, вы ведь его используете, правда?

### Загрузка изображения по ссылке 
Загрузка изображения по ссылке средствами js не представляет проблем и сводится к смене ссылки на изображение, которое отображается на странице. Единственный момент, который нужно учесть, изображение вначале загружается и только потом отображается на странице. Изображение не должно загружаться на глазах пользователя отображаясь на странице по частям.

Необходимо отслеживать процесс загрузки изображения, событие `load`. Как это происходит и где используется можно посмотреть в [примере на codepen](https://codepen.io/irinainina/pen/abzwapy). 

### Загрузка изображения c компьютера
На момент создания js работать с файловой системой не умел. Предполагалось, что это ему не понадобится. Но сейчас 2021 год, у разработчиков намного больше возможностей, и загрузка локальных файлов перестала быть проблемой.

Нам понадобится `<input type="file">` - представитель многочисленного семейства инпутов, предназначенный для загрузки файлов и объект `FileReader` предназначенный для чтения файлов.

Здесь можно посмотреть [пример на codepen](https://codepen.io/irinainina/pen/WNopwQr) с загрузкой изображений с компьютера.

### Сохранение изображений на компьютер
Для сохранения изображений удобнее всего использовать Canvas API.  
С его помошью рисуют в окне браузера, делают игры. Мы с его помощью сохраним изображение.

#### Отображаем изображение на canvas
В html-файле создадим элемент `canvas`
```html
  <canvas></canvas>
```
В css-файле укажем для него стили
```css
  canvas {
    border:1px solid #000;
    width: 500px;
    height: 500px;
  }
```
В js-файле пишем код 
```
  const canvas = document.querySelector('canvas');

  function drawImage() {
    const img = new Image();
    img.setAttribute('crossOrigin', 'anonymous'); 
    img.src = "https://upload.wikimedia.org/wikipedia/commons/c/c9/Зимний_пейзаж.jpg";
    img.onload = function() {
      canvas.width = img.width;
      canvas.height = img.height;
      const ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);
    };  
  }
  drawImage();
```
Мы создали изображение. Указали ему ссылку. Указали что внутренний размер canvas равен размеру изображения. Задали контекст рисования `ctx` и при помощи метода `ctx.drawImage` отобразили на канвасе изображение. 

**Обратите внимание**
- приведённые в примере размеры канваса искажают пропорции изображений, делают их квадратными. Подумайте, как эти размеры можно указать более корректно. Хоть возможно вы решите вообще не отображать canvas на странице, производя все манипуляции с ним скрыто от пользователя.

#### Скачиваем изображение на компьютер
В js-файле пишем функцию
```js
  function downloadImage() {
    const dataURL = canvas.toDataURL("image/jpeg");
    const link = document.createElement("a");
    link.href = dataURL;
    link.download = "image.jpg";
    link.click();
  }
```
Запускаем функцию `downloadImage` внутри функции `drawImage`.   
[Пример кода на codepen](https://codepen.io/irinainina/pen/JjbWXgq)

#### Сохранение изображения вместе с примененными фильтрами
У canvas также есть фильтры. Про фильтры canvas можно почитать [здесь](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/filter)  
