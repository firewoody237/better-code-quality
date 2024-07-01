# Clean Code

- 사실상 주석은 기껏해야 필요악이다.
- 프로그래밍 언어 자체가 표현력이 풍부하다면, 주석은 거의 필요하지 않다.
- 주석은 너무 자주 거짓말을 한다.
- 코드를 깔끔하게 정리하고 표현력을 강화하는 방향으로, 애초에 주석이 필요없는 방향으로 에너지를 쏟아라.
- 진실은 한곳에만 존재한다. 바로 코드다.

## 주석은 나쁜 코드를 보완하지 못한다.
- 코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다.
- 저지른 난장판을 주석으로 설명하지 말고, 깨끗이 치우는 데 시간을 보내라.

## 코드로 의도를 표현하라
- 몇 초만 더 생각하면 코드로 대다수 의도를 표현할 수 있다.

```java
// bad 
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if ((employee.flags & HOULY_FLAG) && (employee.age > 65)) { ... }

// good
if (employee.isEligibleForFullBenefits()) { ... }
```

## 좋은 주석
### 법적인 주석
- 각 소스 파일 첫머리에 주석으로 들어가는 저작권 정보와 소유권 정보는 필요하고도 타당하다.

### 정보를 제공하는 주석
- 가능하다면, 함수 이름에 정보를 담는 편이 더 좋다.

```java
// kkLmm:ss EEE, MMM dd, yyyy 형식이다.
Pattern timeMatcher = Pattern.compile("\\d*:\\d**:\\d* \\w*, \\d*, \\d*");
```

### 의도를 설명하는 주석
- 저자의 의도를 분명하게 드러내는 주석

### 의미를 명료하게 밝히는 주석
- 때때로 모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다.
- 더 나은 방법이 없는지 고민하고 정확히 달도록 각별히 주의한다.

```java
assertTrue(a.compareTo(a) == 0); // a == a
assertTrue(a.compareTo(b) == -1); // a < b
```

### 결과를 경고하는 주석

```java
public static SimpleDateFormat makeStandardHttpDateFormat() {
    // SimpleDateFormat은 스레드에 안전하지 못하다.
    // 따라서 각 인스턴스를 독립적으로 생성해야 한다.
    ...
}
```

### TODO 주석
- TODO로 떡칠한 코드는 바람직하지 않다.
- 그러므로 주기적으로 TODO 주석을 점검해 없애도 괜찮은 주석은 없애길 권한다.

### 중요성을 강조하는 주석

```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 중요하다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식될 수 있다.
```

### 공개 API에서 Javadocs
- 설명이 잘 된 공개 API는 참으로 유용하고 만족스럽다.
- 공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성한다.

## 나쁜 주석
- 대다수의 주석이 속한다.

### 주절거리는 주석
- 주석을 달기로 결정했다면 충분한 시간을 들여 최고의 주석을 달도록 노력한다.
- 저자에게야 의미가 있겠지만 그 의미가 다른 사람들에게는 전해지지 않는다.
- 이해가 안 되어 다른 모듈까지 뒤져야 하는 주석은 독자와 제대로 소통하지 못하는 주석이다.

### 같은 이야기를 중복하는 주석
- 코드와 같은 이야기를 주석에 담지않는게 좋다.

### 오해할 여지가 있는 주석
- 때때로 의도는 좋았으나 프로그래머가 딱 맞을 정도로 엄밀하게는 주석을 달지 못하기도 한다.

### 의무적으로 다는 주석
- 의무적으로 다는 주석은 가치 없을 확률이 높으며, 거짓말을 퍼뜨리고 혼동을 초래한다.

### 있으나 마나한 주석
- 너무 당연한 사실을 언급하며 새로운 정보를 제공하지 못하는 주석.
- `// 기본 생성자`

### 무서운 잡음
- 때로는 Javadocs도 잡음이다.
- 단지 문서를 제공해야 한다는 잘못된 욕심으로 탄생한 잡음이 존재할 수 있다.

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라
- 코드를 작성한 저자는 주석을 먼저 달고 주석에 맞춰 코드를 작성했을지도 모른다.

```java
// bad
// 전역 목록<smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))

// good
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

### 위치를 표시하는 주석
- 소스 파일에서 특정 위치를 표시하려는 주석
- `// Actions ///////////`와 같은 배너는 반드시 필요할 때만, 아주 드물게 사용하는 것이 좋다.

### 닫는 괄호에 다는 주석
- 닫는 괄호에 주석을 닫아야 겠다는 생각이 든다면 대신에 함수를 줄이려 시도하자.

### 공로를 돌리거나 저자를 표시하는 주석
- 오랫동안 코드에 방치되어 점차 부정확하고 쓸모없는 정보로 변하기 쉽다.
- 소스 코드 관리 시스템에 저장하는 편이 좋다.

### 주석으로 처리한 코드
- 그냥 지우는 것이 좋다.
- 소스 코드 관리 시스템이 우리를 대신해 코드를 기억해준다.

### HTML 주석
- HTML 주석은 편집기/IDE에서조차 읽기 어렵다.

### 전역정보
- 주석을 달아야 한다면 근처에 있는 코드만 기술하라.
- 코드 일부에 주석을 달면서 시스템의 전반적인 정보를 기술하지 마라.

```java
// bad
/**
 * 적합성 테스트가 동작하는 포트 : 기본값은 <b>8082</b>.
 */
public void setFitnessePort(int fitnessePort) {
    this.fitnessePort = fitnessePort;
}
```

### 너무 많은 정보
- 주석에다 흥미로운 역사나 관련 없는 정보를 장황하게 늘어놓지 말아라.

### 모호한 관계
- 주석과 주석이 설명하는 코드는 둘 사이 관계가 명확해야 한다.

```java
// bad
/**
 * 모든 픽셀을 담을 만큼 충분한 배열로 시작한다(여기에 필터 바이트를 더한다).
 * 그리고 헤더 정보를 위해 200 바이트를 더한다.
 */
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
```

- 위에서 필터 바이트란 무엇일까?
- 200을 추가하는 이유는?
- 주석 자체가 다시 설명을 요구하는 주석이다.

### 함수 헤더
- 짧은 함수는 긴 설명이 필요 없다.
- 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.

### 비공개 코드에서 Javadocs
- 공개하지 않을 코드라면 Javadocs는 쓸모가 없다.

### 예제
```java
// bad
/**
 * This class Generates prime numbers up to a user specified
 * maximum. The algorithm used is the Sieve of Eratosthenes.
 * <p>
 * Eratosthenes of Cyrene, b. c. 276 BC, Cyrene, Libya --
 * d. c. 194, Alexandria.  The first man to calculate the
 * circumference of the Earth. Also known for working on
 * calendars with leap years and ran the library at Alexandria.
 * <p>
 * The algorithm is quite simple. Given an array of integers
 * starting at 2. Cross out all multiples of 2. Find the next
 * uncrossed integer, and cross out all of its multiples.
 * Repeat untilyou have passed the square root of the maximum
 * value.
 *
 * @author Alphonse
 * @version 13 Feb 2002 atp
 */

public class GeneratePrimes
{
  /**
   * @param maxValue is the generation limit.
   */
  public static int[] generatePrimes(int maxValue)
  {
    if (maxValue >= 2) // the only valid case
    {
      // declarations
      int s = maxValue + 1; // size of array
      boolean[] f = new boolean[s];
      int i;
      // initialize array to true.
      for (i = 0; i < s; i++)
        f[i] = true;

      // get rid of known non-primes
      f[0] = f[1] = false;

      // sieve
      int j;
      for (i = 2; i < Math.sqrt(s) + 1; i++)
      {
        if (f[i]) // if i is uncrossed, cross its multiples.
        {
          for (j = 2 * i; j < s; j += i)
            f[j] = false; // multiple is not prime
        }
      }

      // how many primes are there?
      int count = 0;
      for (i = 0; i < s; i++)
      {
        if (f[i])
          count++; // bump count.
      }

      int[] primes = new int[count];

      // move the primes into the result
      for (i = 0, j = 0; i < s; i++)
      {
        if (f[i])             // if prime
          primes[j++] = i;
      }

      return primes; // return the primes
    }
    else // maxValue < 2
    return new int[0]; // return null array if bad input.
  }

  public static void main(String[] args) {
    for (int i: generatePrimes(50)) {
      System.out.print(i + " ");
    }
  }
}
```

```java
// good
/**
 * This class Generates prime numbers up to a user specified
 * maximum.  The algorithm used is the Sieve of Eratosthenes.
 * Given an array of integers starting at 2:
 * Find the first uncrossed integer, and cross out all its
 * multiples.  Repeat until there are no more multiples
 * in the array.
 */

public class PrimeGenerator {
  private static boolean[] crossedOut;
  private static int[] result;

  public static int[] generatePrimes(int maxValue) {
    if (maxValue < 2)
      return new int[0];
    else {
      uncrossIntegersUpTo(maxValue);
      crossOutMultiples();
      putUncrossedIntegersIntoResult();
      return result;
    }
  }

  private static void uncrossIntegersUpTo(int maxValue) {
    crossedOut = new boolean[maxValue + 1];
    for (int i = 2; i < crossedOut.length; i++)
      crossedOut[i] = false;
  }

  private static void crossOutMultiples() {
    int limit = determineIterationLimit();
    for (int i = 2; i <= limit; i++)
      if (notCrossed(i))
        crossOutMultiplesOf(i);
  }

  private static int determineIterationLimit() {
    // Every multiple in the array has a prime factor that
    // is less than or equal to the root of the array size,
    // so we don't have to cross out multiples of numbers
    // larger than that root.
    double iterationLimit = Math.sqrt(crossedOut.length);
    return (int) iterationLimit;
  }

  private static void crossOutMultiplesOf(int i) {
    for (int multiple = 2 * i;
         multiple < crossedOut.length;
         multiple += i)
      crossedOut[multiple] = true;
  }

  private static boolean notCrossed(int i) {
    return crossedOut[i] == false;
  }

  private static void putUncrossedIntegersIntoResult() {
    result = new int[numberOfUncrossedIntegers()];
    for (int j = 0, i = 2; i < crossedOut.length; i++)
      if (notCrossed(i))
        result[j++] = i;
  }

  private static int numberOfUncrossedIntegers() {
    int count = 0;
    for (int i = 2; i < crossedOut.length; i++)
      if (notCrossed(i))
        count++;

    return count;
  }

  public static void main(String[] args) {
    for (int i : generatePrimes(50)) {
      System.out.print(i + " ");
    }
  }
}
```
