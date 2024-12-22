# 시퀀스 다이어그램 스크립트

## 주문 접수 API

```mermaid
sequenceDiagram
    actor Client
    participant eCommerce
    participant Member
    participant Product
    participant Order

    Client->>eCommerce: [POST] /api/order : 입력(productId, quantity)
    
    eCommerce->>Member: 회원 조회
    activate Member
    break 
        Member-->>Client: [Error] Not Found Member.
    end 
    Member->>eCommerce: 회원 정보 반환
    deactivate Member

    
    eCommerce->>Product: 상품 재고 차감
    activate Product
    break 
        Product-->>Client: [Error] Not Found Product.
    end
    break
        Product-->>Client: [Error] Not Enough Stock.
    end
    Product->>eCommerce: 재고 차감 완료
    deactivate Product

    
    eCommerce->>Order: 주문 정보 저장
    activate Order
    Order->>eCommerce: 주문결과 반환
    deactivate Order

    eCommerce->>Client: 응답(orderId)
```

## 결제 API

```mermaid
sequenceDiagram
    actor Client
    participant eCommerce
    participant Order
    participant Payment

    Client->>eCommerce: [POST] /api/payment : 입력(orderId)
    
    eCommerce->>Order: 주문 정보 조회
    activate Order
    break 
        Order-->>Client: [Error] Not Found Order.
    end 
    Order->>eCommerce: 주문 정보 반환
    deactivate Order
    
    eCommerce->>Payment: 결제 정보 저장
    activate Payment
    Payment->>eCommerce: 결제 결과 반환
    deactivate Payment

    eCommerce->>Client: 응답(paymentId)
```