# Facade 패턴

---
참고  
https://springframework.guru/gang-of-four-design-patterns/facade-pattern/
https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4
https://sourcemaking.com/design_patterns/facade

---
### Facade패턴이란?

퍼사드 패턴은 통합된 인터페이스를 제공한다. 통합된 인터페이스 내부는 하위 시스템들의 인터페이스들로 구성되어 있다. 퍼사드는 하위 시스템을 쉽게 사용할 수 있게 해주는 high-level의 인터페이스다.

### 구성
퍼사드 패턴에는 3가지가 존재한다.
1. 퍼사드
2. 퍼사드를 사용하는 client
3. 퍼사드 내부에서 사용되는 하위시스템

client가 하위시스템을 사용하는 양이 많아서 생기는 복잡성과 의존성들을 줄이기 위해 중간 계층으로 퍼사드를 만들어 사용하게 한다. client는 퍼사드만 알면 되고, 하위 시스템의 복잡성과 의존도는 퍼사드 내부에 구현된다.

![image](https://user-images.githubusercontent.com/54942017/160286181-e46a94ce-9584-46bf-af70-a380edd643bf.png)

### 장점과 단점
장점: client의 하위 시스템에 대한 의존성이 줄어든다.  
단점: 퍼사드가 God object가 될 수 있다.

### 코드
~~~java
class InventoryService {
  public boolean isAvailable(Produtct product) {
    //... product재고를 확인하는 코드
  }
}
class PaymentService {
  public boolean makePayment() {
    //... payment하는 코드
  }
}
class ShippingService {
  public void shipProduct(Product product) {
    //... product를 ship하는 코드
  }
}

class OrderControllerOld {
  //... 각 service 필드 및 주입하는 코드
  
  public void orderProduct(Product product) {
    if (inventoryService.isAvailable(product) && paymentService.makePayment();) {
      shippingService.shipProduct(product);
    }
  }
}

class OrderControllerNew {
  //... facade 필드 및 주입하는 코드
  
  public void orderProduct(Product product) {
    orderFacade.order(product);
  }
}
class OrderFacade {
  //... 각 service 필드 및 주입하는 코드
  
  public void order(Product product) {
    if (inventoryService.isAvailable(product) && paymentService.makePayment();) {
      shippingService.shipProduct(product);
    }
  }
}
~~~

### 왜 굳이 쓰나?
굳이 중간에 왜 끼워 넣었는가 하는 생각이 들 수 있지만, 의미가 있다.  
orderFacade.order()메서드를 사용하는 곳이 많다면, 의미가 더 커진다. service의 사용방법이 달라진다면, 해당 코드를 수정하게 되어야 한다.  
orderControllerOld.orderProduct 메서드 내부에 구현한 코드와 같은 코드가 10곳에서 사용되었다면 10곳의 코드를 수정해야 한다.  
하지만 facade를 사용했다면, facade 한 곳만 수정하면 된다.

