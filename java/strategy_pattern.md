# 전략_패턴

전략 패턴의 정의

```
전략 패턴은 특정한 계열의 알고리즘들을 정의하고
각 알고리즘을 캡슐화하며
이 알고리즘들을 해당 계열 안에서 상호 교체가 가능하게 만든다
```

젼락 패턴은 디자인 패턴의 꽃이라고 불릴 정도로 많이 활용하는 디자인 패턴이며, 기능들마다 행동을 모듈화해서 독립적이고 상호 교체 가능한 것이 전략 패턴입니다.

## 전략 패턴 예제

전략 패턴를 쉽게 이해하기 위하여 간단한 사칙연산 계산기를 예제로 살펴보겠습니다.

### 전략 패턴 적용 전

```
public class Calculator {

  public double plus(double number1, double number2) {
    return number1 + number2;
  }

  public double minus(double number1, double number2) {
    return number1 - number2;
  }

  public double multiply(double number1, double number2) {
    return number1 * number2;
  }

  public double division(double number1, double number2) {
    return number1 / number2;
  }
}
```

```
  @Test
  void test() {
    Calculator calculator = new Calculator();

    double number1 = 10;
    double number2 = 21;
    double result = 0d;
    String operator = "-";

    if ("+".equals(operator)) {
      result = calculator.plus(number1, number2);
    } else if ("-".equals(operator)) {
      result = calculator.minus(number1, number2);
    } else if ("*".equals(operator)) {
      result = calculator.multiply(number1, number2);
    } else if ("/".equals(operator)) {
      result = calculator.division(number1, number2);
    }
    System.out.println(result);
  }
```

두 수를 입력받아 출력하는 간단한 사칙연산 계산기입니다. 계산기의 기능이 확장되어 요구사항이 추가된다면 `if-else` 문을 추가하면 되지만, 코드가 점점 복잡해지며 `개방 폐쇄의 원칙` 에 위배합니다.

> 개방 폐쇠 원칙이란 확장에 대해서는 개방되어야 하지만, 변경에 대해서는 폐쇄되어야 한다는 의미 입니다.

### 전략 패턴 적용 후

기존의 계산 기능을 알고리즘을 추상화하여 `Interface` 을 사용하여 전략을 분리해보겠습니다.

```
public interface CalculatorStrategy {
    double logic(double number1, double number2);
}

public class DivisionCalculator implements CalculatorStrategy {

  @Override
  public double logic(double number1, double number2) {
    return number1 / number2;
  }
}

public class MinusCalculator implements CalculatorStrategy {

  @Override
  public double logic(double number1, double number2) {
    return number1 - number2;
  }
}

public class MultiplyCalculator implements CalculatorStrategy {

  @Override
  public double logic(double number1, double number2) {
    return number1 * number2;
  }
}

public class PlusCalculator implements CalculatorStrategy {
    @Override
    public double logic(double number1, double number2) {
        return number1 + number2;
    }
}
```

계산 기능 자체의 책임을 가지고 있는 `Context` 를 만들어보겠습니다. 해당 클래스에서 사용할 전략은 클라이언트를 통해 전달 받습니다.

```
public class CalculatorContext {

  private CalculatorStrategy calculatorStrategy;

  public CalculatorContext(CalculatorStrategy calculatorStrategy) {
    this.calculatorStrategy = calculatorStrategy;
  }

  public void execute(double number1, double number2) {

    double result = calculatorStrategy.logic(number1, number2);

    System.out.println(result);
  }
}
```

```
  @Test
  void test() {

    CalculatorContext calculatorContext1 = new CalculatorContext(new PlusCalculator());
    calculatorContext1.execute(1, 2);

    CalculatorContext calculatorContext2 = new CalculatorContext(new CalculatorStrategy() {
      @Override
      public double logic(double number1, double number2) {
        return number1 - number2;
      }
    });
    calculatorContext2.execute(2, 1);

    CalculatorContext calculatorContext3 = new CalculatorContext((number1, number2) -> number1 / number2);
    calculatorContext3.execute(10, 2);
  }
```

이제 클라이언트는 필요한 계산 기능의 전략을 생성하여 전략을 실행할 컨텍스트에게 주입합니다. 결국 전략 패턴을 적용하더라도 `if-else` 는 필요에 의해 사용할 수도 있다. 하지만 전략 패턴을 사용하므로써 클라이언트는 필요에 따라 전략을 바꾸게 될 수 있으며 기존 코드를 변경하지 않으면서 유연한 확장을 할 수 있게 되었다.