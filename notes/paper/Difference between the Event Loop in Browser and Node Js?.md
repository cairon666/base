# Web Browser Event Loop

![[Pasted image 20230708110120.png]]
Состоит из:
1) Heap
2) call stack - все фуфнкции которые мы вызывали укладываются в LIFO список(последний вошел, первый вышел)
3) Wep API - API предоставляемый браузером который обеспечивает дополнительный функционал поверх V8 движка.
4) Queues - Очереди которые используемые для выполнения асинхроного кода
	1) Macro tasks - это очередб выполняет функции такие как DOM events, Ajax, setTimeouts и имеет более низкий приоритет чем job queue
	2) Micro tasks - это очередь выполяет функции которые испоьзуют Promise и имеет боее высокий приоритет чем Message Queue

Event Loop проверяет call stack, если он пуст, то переводит функции из очереди в стек вызовов и запускает их. Функции, которые уже присутствуют, имеют более высокий приоритет и запускаются первыми по сравнению с функциями в очереди сообщений.

# NodeJs Event Loop
![[Pasted image 20230708111637.png]]
Состоит из:
1) **Event Queue** - По завершении работы Thread Pool выдается callback function и отправляется в event queue. Если call stack пуст, событие проходит через очередь событий и отправляет функцию обратного вызова в стек вызовов.  
2) **Thread Pool** - Пул потоков состоит из 4 потоков, которые делегируют операции, слишком тяжелые для цикла событий. Операции ввода-вывода, открытие и закрытие соединений, установка таймаутов - пример таких операций.  
3) Event Loop в Node Js имеет различные фазы, которые имеют FIFO очередь обратных вызовов для выполнения. Когда event loop входит в определенную фазу, он выполняет обратные вызовы в очереди этой фазы до тех пор, пока очередь не будет исчерпана и не будет выполнено максимальное количество обратных вызовов, после чего переходит к следующей фазе.
![[Pasted image 20230708112116.png]]
event loop - это бесконечный цикл, который ожидает заданий, выполняет их, а затем "спит", пока не получит новые задания. event loop выполняет задания из очереди только тогда, когда стек пуст. Он обрабатывает сначала самую старую задачу и позволяет нам использовать callbacks и promises.

## Difference between both the event loops?

1) Первое отличие заключается в том, что node использует *thread pool* для управления дисковым вводом/выводом. Он выполняет ввод-вывод и другие timers API асинхронно.
2) Браузер не имеет функции *setImmediate()*. Эта функция выполняется сразу после завершения операции ввода-вывода, если внутри нее находится определенный код, то он будет выполнен первым. В то время как в функции setTimeout() функция обратного вызова выполняется после заданного минимального порогового значения в миллисекундах.
3) Цикл событий Node Js имеет несколько фаз, и каждая фаза обрабатывает определенный тип задач, в то время как браузер имеет очередь микрозадач и макрозадач, в которой все задачи обрабатываются в том порядке, в котором они были помещены в очередь.
4) В браузере, когда вы открываете страницу на вкладке, вы фактически создаете процесс, в котором может быть несколько потоков, таких как JS движок, рендеринг страницы, потоки HTTP запроса и многие другие. В то время как в Node JS, когда вы инициируете клиентский запрос, выполняющий блокирующие операции ввода-вывода, цикл событий выбирает поток и назначает клиентский запрос этому потоку, поскольку цикл событий является однопоточным.

# Comments
Хорошая статья! Хотя, чтобы повторить, я говорил о threadpool, который поддерживает libuv.
Очень краткое объяснение:
Как я уже говорил, в своей основе node ожидает, что ОС сделает всю тяжелую работу. И разные операционные системы имеют разные механизмы для этого, поэтому разработчики node создали библиотеку абстракции под названием libuv. Эта библиотека абстрагирует цикл событий и взаимодействие с ОС (интересно, что вы можете использовать эту библиотеку отдельно).
Теперь, допустим, есть операция, которую node хочет выполнить по-другому, или есть что-то, что ОС не может обработать или не поддерживает. Для управления такими сценариями libuv имеет свой собственный threadpool, и этот threadpool (размер по умолчанию 4) имитирует асинхронное поведение, которое нода ожидает от ОС.




