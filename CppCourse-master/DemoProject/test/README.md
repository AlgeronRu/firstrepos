Итак, если архитектурку мы продумали сносно, то интеграционное тестирование должно пройти на ура. Вообще говоря, в соответствии с TDD, сама архитектура будет разрабатываться уже после того, как в тестах пропишутся основные сценарии и контракты. Но об этом как-нибудь в другой раз.

Ключевые понятия интеграционного тестирования - это заглушки в виде **моков** и **стабов**.

Цель моков - прежде всего, проверить интеграцию компонентов системы:
- что определенные методы вызываются определенное количество раз
- что определенные методы вызываются в определенной последовательности
- что определенные методы вызываются c определенными аргументами и возвращают определенный результат
- и т.д.

Цель стабов - проверить именно что тестируемый объект работает как положено после подстановки ему заглушек.

В наших тестах заглушки на основе интерфейсов ```IDealStrategy``` и ```IGameView``` в разных ситуациях являются одновременно и моками, и стабами.

В интеграционных тестах можно делать акцент на разном:
- мокать состояния, после чего переопределить в контроллере метод runGame(), где явно задать порядок смены состояний (при этом акцент делается на тестировании самих состояний, а не контроллера в целом. Если реализация контроллера поменяется концептуально (например, откажемся вообще от паттерна "Состояние"), то тесты окажутся бесполезны. В нашем примере мы такие тесты не делаем)
- мокать все объекты, используемые контроллером (стратегию выдачи карт, правила игры и представление), чтобы проверить общую логику работы игры. Поэтому контроллер изначально написан по TDD, т.е. так, чтобы он содержал все методы, с помощью которых можно инъектировать необходимые данные с т.з. логики:
    * методы-стратегии ```IDealStrategy``` выдачи карт игроку и дилеру ```giveCardToPlayer()```, ```giveCardToDealer()```, ```initialDeal()```
    * метод ```IGameView``` для получения ответа от игрока ```inputPlayerChoice()``` (в заглушке этот метод будет просто поставлять уже готовые ответы пользователя, как будто он играл сам)

Итак, в наших тестах на контроллере мы проверяем, в каком состоянии он окажется и как изменятся деньги клиента.

На моках мы проверяем, как, когда и с какими параметрами вызываются методы раздачи карт и представления.

В каждом отдельном тесте наворачивается на определенные методы определенная логика средствами google mock. Самым правильным вариантом является делать мок на основе интерфейса (т.е. заглушки создаются для чисто виртуальных функций, ни на какие реализации упор не делается), как это в данном коде и происходит.

О фреймворке Google Mock можно писать много отдельно, чего, впрочем, я делать не буду...

![pic1](https://github.com/ar1st0crat/CppCourse/blob/master/DemoProject/doc/tests.png)