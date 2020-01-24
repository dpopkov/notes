Agile Разработка (Agile Software Development)
=============================================


Agile Design
------------

Понимание общей картины развивается вместе с проектом. С каждой итерацией, команда улучшает дизайн системы так, что он хорош настолько насколько он может быть хорош для системы в ее настоящем состоянии. Команда не тратит много времени на будущие требования и потребности. И также не пытается создать инфраструктуру для поддержки функциональнсоти, которая будет нужна завтра. Фокус делается на текущей структуре системы и на том, чтобы она была настолько хороша, насколько может.


Симптомы плохого дизайна
------------------------

1. Rigidity (Жесткость, Закрепощенность) - Дизайн трудно изменить, потому что каждое изменение заставляет делать другие изменения в других частях системы.
2. Fragility (Хрупкость, Неустойчивость) - Дизайн легко сломать, так как изменения приводят к поломкам в местах, которые не имеют концептуальной связи с изменяемой частью.
3. Immobility (Неподвижность) - Дизайн трудно использовать повторно, так как систему трудно распутать и выделить из нее компоненты, которые могут быть повторно использованы в других системах.
4. Viscosity (Вязкость)- Трудно сделать то что нужно правильным образом, так как сделать что-либо неправильно становится проще, чем сделать это правильно.
5. Needles Complexity (Излишняя Неоправданная Сложность) - Избыточный дизайн, присутствие инфраструктуры, не приносящей прямой выгоды.
6. Needles Repetition (Излишняя Неоправданная Повторяемость) - Система содержит повторяющиеся структуры, которые можно было бы объединить под единой абстракцией.
7. Opacity (Непрозрачность, Неясность, Неопределенность) - Дизайн трудно читать и понимать, он имеет плохую выразительность, плохо выражает свои намерения.

Эти симптомы похожи на code smells, но на более высоком уровне. Это smells, которые пропитывают общую структуру, а не небольшние участки кода.


Принципы, помогающие построить лучший дизайн
--------------------------------------------
1. SRP - The Single Responsibility Principle.
2. OCP - The Open-Close Principle.
3. LSP - The Liskov Substitution Principle.
4. DIP - The Dependency Inversion Principle.
5. ISP - The Interface Segregation Principle.

Design smell это симптом, который может быть измерен. Часто smell вызван нарушением одного или нескольких принципов. Например Rigitity часто является результатом недостаточного внимания принципу OCP.

Принципы применяются для удаления smells. Они не применяются если нет smells. Чрезмерное применение принципов может привести к design smell of Needles Compexity.


Design Smells
-------------

### Rigidity (Жесткость, Закрепощенность)

Закрепощенность  - это тенденция для системы быть трудноизменяемой, даже в простых случаях. Дизайн является закрепощенным если одно изменение вызывает каскад последующих изменений в зависимых модулях. Чем больше модулей должно быть изменено, тем более закрепощенный, жесткий дизайн.

Большинство разработчиков сталкиваются с этой ситуацией. Их просят сделать изменение, которое выглядит простым. Они делают разумное предположение о требуемом объеме работы. Но позднее они обнаруживают последствия изменений, которые они не предвидели. Они вынуждены модифицировать больше модулей, чем первоначально планировали. В итоге изменения отнимают больше времени, чем было запланировано. Когда их спрашивают "Почему?", они отвечают: "Это было намного сложнее, чем я думал!"

### Fragility (Хрупкость, Неустойчивость)

Хрупкость - это тенденции системы ломаться во многих местах после одного изменения. Часто новые проблемы обнаруживаются в местах, которые не имеют концептуальной связи с местом, где производится изменение. Починка этих проблем ведет к еще большему числу проблем, и команда разработчиков становится похожа на собаку преследующую собственный хвост.

Когда хрупкость модуля увеличивается, вероятность что изменение создаст неожиданные проблемы, приближается к уверенности. Такие модули встречаются нередко. Это модули, которые постоянно нуждаются в починке, они никогда не покидают списки багов, разработчики осведомлены о необходимости их редизайна (но никто не хочет это делать), эти модули становятся тем хуже, чем чаще вы их чините.

### Immobilty (Немобильность, Неподвижность)

Дизайн немобилен если он содержит части, которые могли бы быть полезны в других системах, но усилия и риск требуемые для отделения этих частей от исходной системы слишком велики. Это случается очень часто.

### Viscosity (Вязкость)

Вязкость может быть в двух формах: Вязкость Софта и Вязкость Окружения.

Когда разработчики сталкиваются с необходимостью внесения изменений, то они обычно располагают более чем одним способом сделать это. Некоторые способы сохраняют дизайн, некоторые нет (хаки). Если способы сохраняющие дизайн труднее применить, чем хаки, то Вязкость дизайна высока. Сделать неправильно проще, чем сделать правильно. Мы хотим чтобы изменения сохраняющие дизайн было легко делать.

Вязкость окружениея проявляется в медлительности и неэффективности. Например, если компиляция происходит очень долго, то разработчики вынуждены делать те изменения, которые не приводят к большой рекомпиляции, даже если эти изменения нарушают дизайн. Если система контроля версий требует часов чтобы внести в нее несколько файлов, тогда разработчики вынуждены делать такие изменения, которые будет легче закоммитить, несмотря на то, будет ли дизайн сохранен.

В обоих случаях, вязкий проект это проект, в котором дизайн софта трудно сохранить. Мы хотим создавать системы и проекты, в которых сохранять дизайн легко.

