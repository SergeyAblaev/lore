

В этой статье мы рассмотрим, как создавать всплывающие и перекрывающие элементы на React, Angular 1.5 и Angular 2.
Мы рассмотрим примеры создания простого модального окна на каждом из фреймворков.
Весь код в статье написан на typescript. 
Исходный код примеров доступен [на github](https://github.com/rd-dev-ukraine/lore/tree/ui-framework-popup/ui-framework-popup)

# Введение 

Что такое "всплывающие и перекрывающие" элементы? 
Это эмуляция при помощи DOM элементов всплывающих окон (в том числе модальных), выпадающих списков и менюшек, панелек для выбора даты и так далее.

Как правило, для таких элементов применяют абсолютное позиционирование в координатах окна (для модальных окон) при помощи `position: fixed`,
или абсолютное позиционирование в координатах родительских элементов - для меню, выпадающих списков и др. - при помощи `position: absolute`.

Простое размещение всплывающих элеменов возле "родителей" и скрытие/отображение их не работает.
Причина - это родительские компоненты с `overflow`, отличным от `visible`. 
Если абсолютно или фиксированно позиционированный элемент находится в таком контейнере, то все, что выступает за границы контейнера, будет обрезано. 

По-хорошему, эту проблему элегантно мог бы решить Shadow DOM, но пока он не готов.
Поэтому DOM элементы для всплывающих компонентов нужно помещать в `body`, ну или, на худой конец, в специальный контейнер, у родителей которого заведомо нет "обрезающих" стилей.

# Примеры 
       
Все примеры содержат одинаковую верстку, и состоят из поля ввода с текстом и кнопки.
По нажатию на кнопку введенный текст появляется в "модальном" окошке с кнопкой "Закрыть".

Все примеры написаны на typescript. Для запуска примеров используется webpack.
Чтобы запустить примеры, у вас должен быть установлен Node.JS.

Для запуска перейдите в папку с соответствующим примером и выполните в командной строке Node.JS один раз `npm run prepare`, 
чтобы установить глобальные и локальные пакеты. 
Потом выполните `npm run server`. 

После этого откройте в браузере адрес [http://localhost:8080](http://localhost:8080)

# Angular 1.5

Начнем мы со старого доброго Angular 1. 
В версии 1.5 Angular приобрел синтаксический сахар в виде метода `component`, который позволяет создавать компоненты. 
Компоненты это на самом деле директивы, но код их объявления больше ориентирован на создание кирпичиков предметной области приложения,
тогда как директивы больше ориентированы на императивную работу с DOM.
Это нововведение простое, но прикольное, и позволяет объявлять компоненты способом, схожим с Angular 2.
Никаких новых фич, которых не было в директивах, этот способ не привносит, 
но может кардинально повлиять на структуру приложения, особенно, 
если раньше вы пользовались `<div ng-controller="MyController as $c">...</div>`.

От себя я могу добавить, что я пользовался этой возможностью в одном из приложений, и был от нее в восторге. 
Она позволяет создавать компоненты с четким контрактом и высоким потенциалом для повторного использования.
Более того, с этой возможностью я отказался от вынесения HTML разметки компонента в отдельный файл, 
т.к. разметка получается маленькая и аккуратная, и не загромождает исходник компонента. 

В примере я тоже использую эту возможность, поэтому, если вы еще не знакомы с ней, 
почитать можно [здесь](https://docs.angularjs.org/guide/component).

## Динамическая загрузка компонентов