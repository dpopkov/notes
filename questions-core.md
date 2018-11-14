Вопросы
=======


1. Как создать generic array (T[])?

Если массив используется только как private instance field, то можно объявить массив как Object[] и использовать приведение типа при возвращении элементов. Например:

```java
public class ArrayList<E> {
    private Object[] elements;
    
    @SuppressWarnings("unchecked")
    public ArrayList() { elements = (E[]) new Object[10]; }
    
    @SuppressWarnings("unchecked") 
    public E get(int n) { return (E) elements[n]; }
    
    public void set(int n, E e) { elements[n] = e; } // no cast needed
}
```
Если же generic array возвращается методом, то нужно использовать либо ссылку на конструктор массива, либо рефлексию (см. Horstmann, v1, 8.6.6).
