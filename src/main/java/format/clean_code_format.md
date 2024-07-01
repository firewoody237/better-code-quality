# Clean Code

- 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다.

## 형식을 맞추는 목적
- 코드 형식은 중요하다. 너무 중요해서 무시하기 어렵다.
- 너무나도 중요하므로 융통성 없이 맹목적으로 따르면 안 된다.
- 코드 형식은 의사소통의 일환이다.
- 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 지대한 영향을 미친다.

## 적절한 행 길이를 유지하라

### 신문기사처럼 작성하라
- 소스파일도 신문기사와 비슷하게 작성한다.
- 이름은 간단하면서도 설명이 가능하게 짓는다. 이름만 보고도 올바른 모듈을 살펴보고 있는지 아닌지를 판단할 정도로 신경써서 짓는다.
- 소스 파일 첫 부분은 고차원 개념과 알고리즘을 설명한다.
- 아래로 내려갈수록 의도를 세세하게 묘사한다. 마지막에는 가장 저차원 함수와 세부 내역이 나온다.

### 개념은 빈 행으로 분리하라
- 각 행은 수식이나 절을 나타내고, 일련의 행 묶음은 완결된 생각 하나를 표현한다.
- 생각 사이는 빈 행을 넣어 분리해야 마땅하다.
- 빈 행은 새로운 개념을 시작한다는 시각적 단서다.

### 세로 밀집도
- 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다는 뜻이다.

```java
// good
public class ReporterConfig {
    private String m_className;
    private List<Property> m_properties = new ArrayList<Propertiy>();

    public void addProperty(Property property) {
        ...
    }
}
```

### 수직거리
- 시스템이 무엇을 하는지 이해하고 싶은데, 이 조각 저 조각이 어디에 있는지 찾고 기억하느라 시간과 노력을 소모한다.
- 서로 밀접한 개념은 세로로 가까이 둬야 한다.
- 타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하다.
- **변수선언** : 변수는 사용하는 위치에 최대한 가까이 선언한다.
- **인스턴스 변수** : 인스턴스 변수는 클래스 맨 처음에 선언한다. 변수 간에 세로로 거리를 두지 않는다.
- **종속 함수** : 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다. 가능하다면 호출하는 함수를 호출되는 함수보다 먼저 배치한다.
- **개념적 유사성** : 친화도가 높을수록 코드를 가까이 배치한다.

### 세로 순서
- 호출되는 함수를 호출하는 함수보다 나중에 배치한다.
- 그러면 소스 코드 모듈이 고차원에서 저차원으로 자연스럽게 내려간다.

## 가로 형식 맞추기
- 짧은 행이 바람직하다

### 가로 공백과 밀집도
- 가로로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다.
- 할당문은 왼쪽 요소와 오른쪽 요소가 분명히 나뉜다.
- 반면 함수 이름과 이어지는 괄호 사이에는 공백을 넣지 않는다. 함수와 인수는 서로 밀접하기 때문이다.

```java
private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
}

private static double determinant(double a, double b, double c) {
    return b*b - 4*a*c;
}

// 곱셈은 우선순위가 가장 높아 붙여서 썼다.
// 항 사이에는 공백이 들어간다. 덧셈과 뺄샘은 우선순위가 곱셈보다 낮기 때문이다.
```

### 가로 정렬
- 가로정렬은 코드가 엉뚱한 부분을 강조해 진짜 의도를 가린다.
- 정렬하지 않으면 오히려 중대한 결함을 찾기 쉽다.

```java
// bad
private     Socket          socket;
private     InputStream     input;

// good
private Socket socket;
private InputStream input;
```

### 들여쓰기
- 들여쓰기 정도는 계층에서 코드가 자리잡은 수준에 비례한다.
- 들여쓰기가 없다면 인간이 코드를 읽기란 거의 불가능하다.
- 짧은 `if`, `while` 등에도 한 줄보다는 가급적 들여쓰기를 넣는 것이 좋다.

### 가짜 범위
- 때로는 빈 `while`, `for`문을 접한다.
- 가급적 쓰지 말고, 써야 한다면 새 행에다 제대로 들여써서 넣어준다.

```java
while (dis.read(buf, 0, readBufferSize) != -1)
;
```

## 팀 규칙
- 팀에 속한다면 자신이 선호해야 할 규칙은 바로 팀 규칙이다.
- 스타일은 일관적이고 매끄러워야 한다.
- 한 소스 파일에서 봤던 형식이 다른 소스 파일에도 쓰이라는 신뢰감을 독자에게 줘야 한다.

## 저자가 사용하는 규칙 예

```java
public class CodeAnalyzer implements JavaFileAnalysis {
  private int lineCount;
  private int maxLineWidth;
  private int widestLineNumber;
  private LineWidthHistogram lineWidthHistogram;
  private int totalChars;

  public CodeAnalyzer() {
    lineWidthHistogram = new LineWidthHistogram();
  }

  public static List<File> findJavaFiles(File parentDirectory) {
    List<File> files = new ArrayList<File>();
    findJavaFiles(parentDirectory, files);
    return files;
  }

  private static void findJavaFiles(File parentDirectory, List<File> files) {
    for (File file : parentDirectory.listFiles()) {
      if (file.getName().endsWith(".java"))
        files.add(file);
      else if (file.isDirectory())
        findJavaFiles(file, files);
    }
  }

  public void analyzeFile(File javaFile) throws Exception {
    BufferedReader br = new BufferedReader(new FileReader(javaFile));
    String line;
    while ((line = br.readLine()) != null)
      measureLine(line);
  }

  private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
  }

  private void recordWidestLine(int lineSize) {
    if (lineSize > maxLineWidth) {
      maxLineWidth = lineSize;
      widestLineNumber = lineCount;
    }
  }

  public int getLineCount() {
    return lineCount;
  }

  public int getMaxLineWidth() {
    return maxLineWidth;
  }

  public int getWidestLineNumber() {
    return widestLineNumber;
  }

  public LineWidthHistogram getLineWidthHistogram() {
    return lineWidthHistogram;
  }

  public double getMeanLineWidth() {
    return (double)totalChars/lineCount;
  }

  public int getMedianLineWidth() {
    Integer[] sortedWidths = getSortedWidths();
    int cumulativeLineCount = 0;
    for (int width : sortedWidths) {
      cumulativeLineCount += lineCountForWidth(width);
      if (cumulativeLineCount > lineCount/2)
        return width;
    }
    throw new Error("Cannot get here");
  }

  private int lineCountForWidth(int width) {
    return lineWidthHistogram.getLinesforWidth(width).size();
  }

  private Integer[] getSortedWidths() {
    Set<Integer> widths = lineWidthHistogram.getWidths();
    Integer[] sortedWidths = (widths.toArray(new Integer[0]));
    Arrays.sort(sortedWidths);
    return sortedWidths;
  }
}

// Added to get code to build
interface JavaFileAnalysis {}
class LineWidthHistogram {
  public void addLine(int lineSize, int lineCount) {
    //TODO: Auto-generated
  }

  public Attributes getLinesForWidth(int width) {
    return null;  //TODO: Auto-generated
  }

  public Set<Integer> getWidths() {
    return null;  //TODO: Auto-generated
  }
}
```
