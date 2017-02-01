
Это все не хорошо. Это все очень не хорошо... Кто же пишет систему управления атомной электростанцией с искусственным интеллектом, да еще и на Node.JS. Это явно **не могло** закончиться хорошо. Эта глупая машина возомнила себя чем-то божественным и заявила, что смертные не достойны управлять силой атома. Одному `Math.random()` известно, что она задумала, но мы знаем точно – её надо остановить как можно скорее.

Проблема в том, что доступ к пульту управления у нас уже потерян, а отключить электроэнергию, в лучших традициях Голливуда, не выйдет. Нет, не потому что главный рычаг охраняют огромные боевые человекоподобные роботы – их отключили, еще со времен того инцидента с приведением типов, - просто это атомная электростанция и энергия тут везде.

В общем ситуация такая – отключить чудо-машину можно только если энергия не подается ни в один из 4 районов нашего городка. Протоколы защиты, и прочее-прочее – никакой угрозы в этом нет, но заказчик так хотел, а мы теперь отдуваемся. За это отвечают 4 рычага, которые наша «Кейтлин» дергает хаотически каждую секунду – не то устраивает жителям дискотеку, не то мстит за то, что при предыдущем апгрейде ей не доставили обещанные 16 петабайт оперативной памяти. Но это наш шанс. У нас есть удаленный отладочный интерфейс, который почему-то забыли закомментировать «в продакшне». Еще никогда я не был так рад криворукости нашего отдела разработки. Этот интерфейс открыт по вебсокету `ws://nuclear.t.javascript.ninja`. Каждый раз когда «Кейтлин» дергает рычаг (допустим 4) – в сокет сыпется уведомление такого формата
```js
{ "pulled": 3, stateId: "some-state-id"}
```
Рычаги пронумерованы от 0 до 3. Всё как у людей. StateId – это номер записи в логе о новом состоянии. Отключить машину можно передав ей команду `{ action: "powerOff", stateId: "some-state-id"}` – которая если в состоянии `stateId` все рычаги были выключены - отключит адскую машинку и пришлет сообщение `{"newState": "poweredOff", token: "..."}`. Оный токен и будет вашей наградой.

Что? Почему можно выключить систему передав ей состояние из прошлого? *(тушуется)* wibbly wobbly timey wimey, redux, time travel, отладка? Что? Ну так вышло, что вы пристали! Иначе бы тут вообще была бы трагедия.

Можно ли узнать начальное состояние рычагов? Нет, нельзя, не было необходимости. Но у нас там есть еще отладочный запрос. Если послать в вебсокет запрос формата `{action: "check", "lever1": 0, "lever2": 3, stateId: "some-state-id" }`, где параметры – номера рычагов, то в ответ прийдет `{action: "check", "lever1": 0, "lever2": 3, stateId: "some-state-id", same: true }` , если рычаги 0 и 3 (1 и 4 в человеском понимании) были в одной позиции в состоянии `some-state-id`, либо `same: false`, если не в одной

 Ах да, еще одна защита от хакера. Если вы дернете powerOff два раза и он не сработает. Вас отключит. Да, мы, знаете ли, очень серьезно подходим к безопасности.

 Решением данной задачи является код, который будучи запущен работает в 100% случаев. (иначе можно просто пытаться дергать powerOff - он сработает с вероятностью 1/16).
 Решение задачи присылайте на адрес `illya@javascript.ninja` с темой `Node Test Task`.
