ООД
===

* [1 - Что такое SOLID?](#q01)
* [2 - Для чего используется JavaDoc?]()
* [3 - Как писать JavaDoc?]()
* [4 - Что такое Java code conventions?]()
* [5 - Расскажите про принципы составления Java класса]()
* [6 - Что такое SRP?]()


1 - Что такое SOLID?<a href="q01"></a>
--------------------------------------
SOLID - это акроним представляющий 5 принципов разработки.  
S - Single Responsibility Principle,  
O - Open Closed Principle,  
L - Liskov Substitutin Principle,  
I - Interface Segregation Principle,  
D - Dependency Inversion Principle.  
Эти принципы были собраны и организованы Робертом Мартином в книге "Agile Software Development. Principles, Patterns, and Practices". Сами принципы были еще ранее сформулированны рядом известных разработчиков и исследователей начиная с 70-х годов.


6 - Что такое SRP?<a href="q06"></a>
------------------------------------
SRP - Single Responsibiltiy Principle.  
Each class in your system should have only one responsibility.  
Alternative definition: You should be able to describe what each class does withou saying "and".  
Better definition: Each class should have only one reason to change.  
Class follows SRP if you can't come up with more than one reason for a class to change.
Responsibility == reason to change.  


SRP violations lead to excessive coupling.

