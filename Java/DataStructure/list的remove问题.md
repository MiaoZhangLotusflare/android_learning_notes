# Java List 的 remove() 方法陷阱以及性能优化

## 问题

Java List 在进行 remove() 方法是通常容易踩坑，主要有一下几点：

**循环时，删除某个元素后，因为删除元素后，后面的元素都往前移动了一位，而你的索引+1，所以实际访问的元素相对于删除的元素中间间隔了一位。**

1. 使用 for 循环不进行额外处理时（**错误**）

```java
//错误的方法
for(int i=0;i<list.size();i++) {
	if(list.get(i)%2==0) {
		list.remove(i);
	}
}
```

2. 使用 foreach 循环（**错误**）

```java
for(Integer i:list) {
    if(i%2==0) {
     	list.remove(i);
    }
}
```

上面的两种情况都会抛出异常：java.util.ConcurrentModificationException；
foreach 的本质是使用迭代器实现，每次进入 for (Integer i:list) 时，会调用 ListItr.next() 方法，继而调用 checkForComodification() 方法， checkForComodification() 方法对操作集合的次数进行了判断，如果当前对集合的操作次数与生成迭代器时不同，抛出异常

```java
public E next() {
	checkForComodification();
	if (!hasNext()) {
		 throw new NoSuchElementException();
	}
	 lastReturned = next;
	next = next.next;
	nextIndex++;
	return lastReturned.item;
 }
 // checkForComodification()方法对集合遍历前被修改的次数与现在被修改的次数做出对比
final void checkForComodification() {
	  if (modCount != expectedModCount) {
	  		 throw new ConcurrentModificationException();
	  }
             
  }
```

## 解决方法

- 使用 for 循环，并且同时改变索引；（**正确**）

```java
//正确
for(int i=0;i<list.size();i++) {
	if(list.get(i)%2==0) {
		list.remove(i);
		i--;//在元素被移除掉后，进行索引后移
	}
}
```

- 使用 for 循环，倒序进行；（**正确**）

```java
//正确
for(int i=list.size()-1;i>=0;i--) {
	if(list.get(i)%2==0) {
		list.remove(i);
	}
}
```

- 使用 while 循环，删除了元素，索引便不 +1，在没删除元素时索引 +1（**正确**）

```java
//正确
int i=0;
while(i<list.size()) {
	if(list.get(i)%2==0) {
		list.remove(i);
	}else {
		i++;
	}
}
```

* 使用迭代器方法（**正确,推荐**）
  只能使用**迭代器的** remove() 方法，使用**列表的** remove() 方法是错误的

```java
//正确，并且推荐的方法
Iterator<Integer> itr = list.iterator();
while(itr.hasNext()) {
	if(itr.next()%2 ==0)
		itr.remove();
}
```

## 优化

下面来谈谈当数据量过大时候，需要删除的元素较多时，如何用迭代器进行性能的优化，对于 ArrayList 这几乎是致命的，从一个 ArrayList 中删除批量元素都是昂贵的时间复杂度为O(n²)，那么接下来看看 LinkedList 是否可行。LinkedList 暴露了两个问题，一个：是每次的 Get 请求效率不高，而且，对于 remove 的调用同样低效，因为达到位置 i 的代价是昂贵的。

使用迭代器的方法删除元素，对于 LinkedList，对该迭代器的 remove() 方法的调用只花费常数时间，因为在循环时该迭代器位于需要被删除的节点，因此是常数操作。对于一个 ArrayList，即使该迭代器位于需要被删除的节点，其 remove() 方法依然是昂贵的，因为数组项必须移动。下面贴出示例代码以及运行结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019052517530268.png)

```java
public class RemoveByIterator {

	public static void main(String[] args) {
		
		List<Integer> arrList1 = new ArrayList<>();
		for(int i=0;i<100000;i++) {
			arrList1.add(i);
		}
		
		List<Integer> linList1 = new LinkedList<>();
		for(int i=0;i<100000;i++) {
			linList1.add(i);
		}

		List<Integer> arrList2 = new ArrayList<>();
		for(int i=0;i<100000;i++) {
			arrList2.add(i);
		}
		
		List<Integer> linList2 = new LinkedList<>();
		for(int i=0;i<100000;i++) {
			linList2.add(i);
		}
		
		removeEvens(arrList1,"ArrayList");
		removeEvens(linList1,"LinkedList");
		removeEvensByIterator(arrList2,"ArrayList");
		removeEvensByIterator(linList2,"LinkedList");
		
	}
	public static void removeEvensByIterator(List<Integer> lst ,String name) {//利用迭代器remove偶数
		long sTime = new Date().getTime();
		Iterator<Integer> itr = lst.iterator();
		while(itr.hasNext()) {
			if(itr.next()%2 ==0)
				itr.remove();
		}
		
		System.out.println(name+"使用迭代器时间:"+(new Date().getTime()-sTime)+"毫秒");
	}
	
	public static void removeEvens(List<Integer> list , String name) {//不使用迭代器remove偶数
		long sTime = new Date().getTime();
		int i=0;
		while(i<list.size()) {
			if(list.get(i)%2==0) {
				list.remove(i);
			}else {
				i++;
			}
		}
	
		System.out.println(name+"不使用迭代器的时间"+(new Date().getTime()-sTime)+"毫秒");
	}
}
```

## 参考文章

1. [Java List的remove()方法陷阱以及性能优化](https://blog.csdn.net/wsdfym/article/details/90544839)



