# 01-01. Generic

## Q. 자바의 Generic에 대해 설명해주세요.

자바의 Generic은 **"이 클래스나 메서드는 나중에 실제로 사용할 때 타입을 정해줄게요"**&#xD558;고 작성할 때 데이터 타입을 정하지 않는 기능입니다.



예를 들어 RPG게임의 던전을 설계할 때 '아이템 상자(ItemBox)'가 하나 있다고 합시다. 근데 이 상자는 어떤 아이템을 담을지 미리 정해두지 않았어요.

어떤 던전은 '장비'아이템만 들어갈 수도 있고, 어떤 던전은 '포션' 아이템만 들어갈 수 있죠.

```java
public class ItemBox<T> {
    private List<T> items = new ArrayList<>();
    public void addItem(T item) { items.add(item); }
    public T getItem(int idx) { return items.get(idx); }
}
```

만약 던전을 설계할 때 `"아, 이번엔 이 상자에 포션만 담겠다"` 한다면

```java
ItemBox<Potion> potionBox = new ItemBox<>();
potionBox.addItem(new Potion("체력 회복", 50));
```

이런식 으로 이 상자에는 '포션'만 들어가게 하고, 컴파일러가 "이제 이 박스는 Potion 전용이니까, 다른 아이템은 넣지마!"라고 체크해 줍니다.

여기에 장비 아이템을 넣으려 하면 컴파일 단계에서 에러가 발생하겠죠.



자바에서 많이쓰는 컬렉션에도 똑같이 적용됩니다.

```java
List<Player> players = new ArrayList<>();
List<Monster> monsters = new ArrayList<>();
```

이렇게 제네릭으로 선언해두면 "플레이어 리스트"엔 몬스터를 넣지 못하게 컴파일러가 잡아주고,

꺼낼 때도 매번 형변환할 필요가 없으니 깔끔하게 꺼낼 수 있습니다.

즉, 제네릭을 사용하면 코드 재사용성이 높아지고, 컴파일 시 타입 안전성을 확보할 수 있습니다.



## Q. Object를 사용하면 String을 포함한 모든 타입을 다 저장할 수 있을텐데, Generic를 꼭 써야하는 이유가 있나요?

`Object`를 사용하면 어떤 타입이든 저장할 수 있지만, 꺼낼 때 항상 `Object` 타입으로 반환되기 때문에 추가 작업을 위해 **명시적 형변환(casting)**&#xC774; 필요합니다.



예를 들어, `Object` 컬렉션에서 `Integer`를 꺼내서 연산하려면 매번 \`(Integer)로 형변환해야 하고,

잘못된 타입이 들어왔을 경우 런타임에 `ClassCastException` 이 발생할 수 있습니다.

```java
List<Object> list = new ArrayList<>();
list.add("Hello");
list.add(123);

// 꺼낼 때마다 캐스팅이 필요
Integer num = (Integer) list.get(1); // OK
Integer bad = (Integer) list.get(0); // 런타임 에러!
```

제네릭을 사용하면 컴파일 단계에서 "이 컬렉션에는 오직 `Integer`만 들어올 수 있다"는 제약을 걸어 두므로,

1. 명시적 캐스팅이 불필요하며,
2. 타입 안정성을 확보하고,
3. 런타임 오류를 방지할 수 있습니다.

```ㅓㅁㅍㅁ
List<Integer> list = new ArrayList<>();
list.add(123);
// list.add("Hello"); // 컴파일 에러로 미리 차단

Integer num = list.get(0); // 캐스팅 불필요
```



즉, Object 기반 코드는 유연하지만 **“유연함”**&#xC774; 곧 `ClassCastException` 같은 위험을 동반합니다.

제네릭을 쓰면 **“원하는 타입만 저장·조회할 수 있다”**&#xB294; 보장이 컴파일러 단계에서 이루어지기 때문에, 코드가 더 안전하고 가독성·유지보수성도 크게 향상됩니다.



## Q. 추가로 와일드카드에 대해서 설명해주세요.

제네릭 와일드카드는 **제네릭 타입에서 특정 타입을 명시하지 않고, 어떤 타입이 올 수 있는지에 대한 제약을 표현하는 방법**입니다.

마치 정규 표현식에서 와일드카드 문자(\*)가 어떤 문자든지 대표하는 것처럼, 제네릭에서도 와일드카드(?)가 다양한 타입을 나타냅니다.

세가지 종류가 있는데 게임으로 풀어서 설명해보겠습니다.



먼저 **“비제한 와일드카드”**&#xB77C;고 하는 `?`는 그야말로 **“던전 안에 어떤 아이템이 담긴 상자”**&#xB77C;고 볼 수 있습니다.

던전 안에 상자가 있고, 거기 뭔가 들었는지 열어서 꺼내볼 수는 있지만 다시 넣을 순 없습니다.

상자에 아이템을 추가하려 하면 “어, 이 상자가 어떤 타입인지 몰라서 안 돼요” 하고 막히게 됩니다.

```java
public void printAll(List<?> list) {
    for (Object o : list) {
        System.out.println(o);
    }
    // list.add(...) 은 컴파일 에러
}
```



다음으로 **“상한 제한 와일드카드”**&#xC778; `? extends Equipment`는 **“장비류 아이템” 전용 상자**를 열어보는 거랑 비슷하다고 볼 수 있습니다.

검이든 방어구든 뭐가 들어있는 "장비"라는 최상위 개념으로만 꺼낼 수 있습니다.&#x20;

이 상한 제한 와일드카드도 비제한 와일드카드 처럼 다시 새로 장비를 넣을 순 없습니다.

```java
public double sum(List<? extends Number> nums) {
    double s = 0;
    for (Number n : nums) {
        s += n.doubleValue();
    }
    // nums.add(...) 은 컴파일 에러
    return s;
}
```



마지막으로 **“하한 제한 와일드카드”**&#xC778; `? super Potion`은 **“포션류 전용 진열대”**&#xC640; 비슷하다고 볼 수 있습니다.

체력포션이든 마나포션이든, 또는 나중에 새로운 포션이 나와도 포션의 상위 개념이라면 다 집어넣을 순 있습니다.

그런데 꺼낼 때는 정확히 어떤 포션인지 보장 못 하기 때문에, 그냥 ‘아이템(Object)’ 수준으로만 꺼낼 수 있습니다.

```java
public void fillWithInts(List<? super Integer> ints) {
    ints.add(1);
    ints.add(2);
    // Integer n = ints.get(0); // 컴파일 에러
    Object o = ints.get(0);
}
```



이렇게 와일드카드를 쓰면, 메서드가 ‘아이템을 꺼내기만 할 것인지’ 아니면 ‘아이템을 넣기만 할 것인지’를 선언적으로 구분할 수 있어, 코드의 의도가 훨씬 명확해집니다.



## Q. 자바 프로그램이 동작할 때 제네릭은 어떻게 작동하나요? (원시타입으로 제네릭을 쓰는건 왜 안되나요?)

우리가 자바에서 코드를 작성할 때는 제네릭 타입을 사용하지만, 실제로 컴파일되어 실행할 때는 이 타입 정보가 제거 됩니다. 이것을 타입 소거(Type Erasure)라고 합니다.

이것은 자바의 하위 호환성 때문입니다. Java5에서 제네릭이 도입됐을 때, 이전 버전의 코드들과 호환성을 유지하면서도 새로운 기능을 추가해야 했기 때문입니다.

그래서 실제로 JVM이 실행할 때는 제네릭 타입 정보가 전부 Object로 변환되고, 필요한 곳에서 자동으로 형변환이 일어납니다.

이때 컴파일러가 미리 타입 체크를 해주기 때문에, 실행 시점에서 형변환 오류가 발생하지는 않습니다.

또한 원시타입은 형변환이 불가능 하므로 제네릭으로 원시 타입을 직접 사용할 수는 없습니다.

\[ 예시 ]

```java
// 우리가 작성한 코드
ArrayList<String> list = new ArrayList<>();
list.add("Hello");
String str = list.get(0);

// 컴파일 후 실제 실행되는 코드
ArrayList list = new ArrayList();
list.add("Hello");
String str = (String) list.get(0);
```
