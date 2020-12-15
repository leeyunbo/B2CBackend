# B2CBackend
Java를 활용한 B2CBackend 개발

### 첫번째 문제(순수 JAVA를 이용한 구현) 
```java
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository = new MemoryMemberRepository();
    // private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);

        //할인 정책이 변경되어도 OrderService는 변경될 필요가 없음 (단일 책임의 원칙)
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```
##### Q. `RateDiscountPolicy`(정액 할인법)과 `FixDiscountPolicy`(정률 할인법)을 구현한 후, 필요할때 마다 변경할 수 있도록 역할과 구현을 확실하게 분리하여 설계를 진행하였다. 그렇다면 OCP와 DIP를 지키게 된걸까? 
##### A. `OrderServiceImpl`에서 코드의 변경이 이루어질 뿐만 아니라(OCP 위반), `OrderServiceImpl`이 인터페이스인 `DiscountPolicy`, 구현 객체인 `RateDiscountPolicy`에 동시에 의존(DIP 위반)한다. 따라서 OCP, DIP를 지킨다고 할 수 없다.

##### Q. 해결 방법은?
##### A. `OrderServiceImpl`이 DIP를 지키기 위해서는 인터페이스(추상화)에만 의존해야 한다. 따라서 구현 객체를 생성하고 주입시켜줄 수 있는 제 3자가 필요하다.
