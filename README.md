# ErrorHandling

商品入時のエラーハンドリング

### モデル

```
struct Store {
    let name: String
    var isOpen: Bool
}

struct Item {
    let price: Double
    let name: String
    var store: Store
    var status: StockStatus
}

enum StockStatus {
    case outOfStock
    case inStock
}

protocol BuyProtocol {
    func buy(item: Item) throws
}

struct User: BuyProtocol {
    let name: String
    var money: Double
    
    internal func buy(item: Item) throws {
        guard item.status != .outOfStock else {
            throw BuyError.ItemNotAvailable
        }
        guard item.store.isOpen else {
            throw BuyError.StoreClosed
        }
        guard self.money > item.price else {
            throw BuyError.LackOfMoney
        }
        print("Thank you for buying \(item.name)")
    }
}
```

### エラータイプ

```
func errorHandle(_ error: BuyError) {
    switch error {
    case .LackOfMoney:
        print("you don't have enough money")
    case .ItemNotAvailable:
        print("Out of stock")
    case .StoreClosed:
        print("Store is closed")
    }
}

enum BuyError: Error {
    case LackOfMoney
    case ItemNotAvailable
    case StoreClosed
}
```

```
let user = User(name: "user1", money: 900)
let store = Store(name: "myStore", isOpen: true)
var item = Item(price: 100, name: "item1", store: store, status: .inStock)
var item2 = Item(price: 999, name: "item2", store: store, status: .inStock)
var item3 = Item(price: 999, name: "item3", store: store, status: .outOfStock)

do {
    try user.buy(item: item)
} catch let error as BuyError {
    errorHandle(error)
}

do {
    try user.buy(item: item2)
} catch let error as BuyError {
    errorHandle(error)
}

do {
    try user.buy(item: item3)
} catch let error as BuyError {
    errorHandle(error)
}

item.store.isOpen = false

do {
    try user.buy(item: item)
} catch let error as BuyError {
    errorHandle(error)
}
```