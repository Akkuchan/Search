# 배열과 List

## 배열을 리스트로
  - 배열을 리스트로 바꾸는 방법은 3가지가 있다.
    1. Arrays.asList()
    2. new ArrayList<>(Arrays.asList())
    3. Collectors.toList()

###  Arrays.asList(array a)
    ```java
    import java.util.Arrays;
    import java.util.List;
    public class ArrayConversion {    
      public static void main(String[] args) {        
        String[] arr = { "A", "B", "C" };         
        // 배열 -> List로 변환        
        List<String> list = Arrays.asList(arr);
        System.out.println(list);        
        System.out.println(list.get(0)); // "A"        
        System.out.println(list.get(1)); // "B"        
        System.out.println(list.get(2)); // "C"    }}
          출처: https://hianna.tistory.com/551 [어제 오늘 내일:티스토리]
    //[A, B, C]
    // A
    // B
    // C

    ```
  - Arrays.asList()를 이용해 배열을 List로 변환할 수 있다.
  - 다만 고정된 길이의 원래 배열(specified array)의 list view를 반환한다.
  - 때문에 고정된 길이만 사용할 때는 상관없지만 list의 크기를 변경시켜야할 때는 제한이 있다.

  - 배열의 값이 중간에 변경되면 이전에 해당 배열을 통해 생성한 list도 값이 변동된다.
  ``` java
  import java.util.Arrays;
  import java.util.List;
  public class ArrayConversion {    
    public static void main(String[] args) {        
      String[] arr = { "A", "B", "C" };// 배열 -> List로 변환        
      List<String> list = Arrays.asList(arr); // arr 배열 값 변경        
      arr[0] = "D";         
      System.out.println(list.get(0)); // "D"        
      System.out.println(list.get(1)); // "B"        
      System.out.println(list.get(2)); // "C"
  --------------------------------------------------------
      list.set(0, "D");        
      System.out.println(Arrays.toString(arr)); // ["D", "B", "C"]         
      System.out.println(list.get(0)); // D        
      System.out.println(list.get(1)); // B        
      System.out.println(list.get(2)); // C    
    }
  }
출처: https://hianna.tistory.com/551 [어제 오늘 내일:티스토리]
 ```
 - 고정된, 원래 배열의 list view이다
 - 따라서 원본값을 list로 볼 수 있게<u>만</u>해주는 기능의 메서드인 것이다.
 - list의 값을 바꿔주었더라도 동일하게 배열의 값이 변동된 것을 확인할 수 있다.

```java
    public class ArrayConversion {    
      public static void main(String[] args) {        
        String[] arr = { "A", "B", "C" };// 배열 -> List로 변환        
        List<String> list = Arrays.asList(arr);// list에 값 추가        
        list.add("D");         
        System.out.println(list.get(0));        
        System.out.println(list.get(1));        
        System.out.println(list.get(2));        
        System.out.println(list.get(3));    
      }
    }
```
  - Array.asList()는 단순히 원래배열을 list형태로 읽기전용으로 만든 것이기에 add()를 통해 값을 추가할 수 없다(고정된 배열에 기반하기에)
  - 이를 실행하면 Exception in thread "main" java.lang.UnsupportedOperationException 예외가 발생한다. 즉 해당 매서드에서는 지원하지 않는 기능(요소추가)을 명령했기에 예외가 발생한다는 의미이다.


### new ArrayList<>(Arrays.asList())
  - 위에서 언급한 단점들을 극복하기 위해 일반적으로 주어진 배열의 값을 담고 있는 새로운 객체를 생성하여 값을 활용한다.
  - 즉 원배열과의 동기화를 막고자 복사본을 새로 만들어 활용한다는 의미이다.
  ```java
  public static void main(String[] args) {        
    String[] arr = { "A", "B", "C" };
    // 배열 -> List로 변환        
    List<String> list = new ArrayList<>(Arrays.asList(arr));
    // arr 배열 값 변경        
    arr[0] = "0";// list에 값 추가        
    list.add("D");         
    System.out.println(list.get(0));  // A        
    System.out.println(list.get(1));  // B        
    System.out.println(list.get(2));  // C        
    System.out.println(list.get(3));  // D    }

  ```
  - List는 기본적으로 인터페이스이기에 객체를 만들어야만(즉 내부의 추상메서드를 구현해야) 활용이 가능한데 이것을 ArrayList라는 클래스를 인스턴화하여 배열의 값들을 따로 활용할 수 있게 된다는 것.


### Collectors.toList()
    - stream을 활용하여 배열의 값들을 활용할 수 있게 되었다.

    ```java
    public static void main(String[] args) {        
      String[] arr = { "A", "B", "C" };         
      // 배열 arr을 collect()로 파라미터 타입의 데이터로(List로) 변환        
      List<String> list = Stream.of(arr).collect(Collectors.toList());
      /        
      System.out.println(list.get(0)); // A        
      System.out.println(list.get(1)); // B        
      System.out.println(list.get(2)); // C    
    }

    ```
  - collect()의 파리미터로 Collectors.toList()를 대입해 주어진 스트림의 요소(arr:배열)을 원하는 타입(List)로 변환 해 준다.
  ※ collect()는 Stream의 데이터를 변형 등의 처리를 하고, 원하는 자료형으로 변환해 줍니다.


## List를 배열로
  - list를 배열로 변환하는 경우도 존재한다. 이 경우
  - toArray(Obj[] o)를 사용한다.

  ```java
  public static void main(String[] args) {         
    // ArrayList 준비        
    ArrayList<String> arrList = new ArrayList<String>();        
    arrList.add("A");        
    arrList.add("B");        
    arrList.add("C");         
    // ArrayList를 배열로 변환        
    int arrListSize = arrList.size();        
    String arr[] = arrList.toArray(new String[arrListSize]);         
    // 변환된 배열 출력        
    System.out.println(Arrays.toString(arr));     
    //[A, B, C]
  }

  ```
  - 주어진 ArrayList타입의 arrlist에 .toArray()를 붙히고 배열 객체를 파라미터로 하여금 새로운 String 배열 arr을 생성할 수 있다.
  - 파라미터는 새로 값을 담을 배열 객체를 넣어주면 된다. 이때 새로운 배열 객체의 크기를 따로 지정하지 않고 0으로 정하여도 알아서 list 사이즈에 맞게 배열을 만들어준다.
  - list 사이즈보다 객체 배열의 크기가 크다면 나머지 인덱스값은 null이 된다.
