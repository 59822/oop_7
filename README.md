# Reto 7
From Reto 3 and implement a new Class that creates and iterable with all the items in an order, it should allow looping and contain all item attributes.

``` python
class MenuItem:
    def __init__(self, name, price, size):
        self._name = name
        self._price = price
        self._size = size

    def get_name(self):
        return self._name

    def set_name(self, value):
        self._name = value

    def get_price(self):
        return self._price

    def set_price(self, value):
        self._price = value

    def get_size(self):
        return self._size

    def set_size(self, value):
        self._size = value

    def calculate_total_price(self, quantity):
        total_price = self._price * quantity
        self._price = total_price
        return total_price


class Beverage(MenuItem):
    def __init__(self, name, price, size):
        super().__init__(name, price, size)


class Appetizer(MenuItem):
    def __init__(self, name, price, size):
        super().__init__(name, price, size)


class MainCourse(MenuItem):
    def __init__(self, name, price, size, meat=True):
        super().__init__(name, price, size)
        self._meat = meat

    def get_meat(self):
        return self._meat

    def set_meat(self, value):
        self._meat = value

    def calculate_total_price(self, quantity):
        total_price = self._price * quantity
        if any(isinstance(item, Beverage) for item in order.items):
            total_price *= 0.9  
        return total_price


class Order:
    def __init__(self):
        self.items = []

    def add_item(self, item):
        self.items.append(item)

    def calculate_total_bill(self):
        total_bill = 0
        for item in self.items:
            total_bill += item.get_price()
        return total_bill

    def discount(self, amount):
        prix = self.calculate_total_bill()
        prix_discount = prix * (amount / 100)
        prix_final = prix - prix_discount
        return prix_final

    def get_items_with_prices(self):
        items_with_prices = "\n".join([f"{item.get_name()}: ${item.get_price()}" for item in self.items])
        return items_with_prices

    def __iter__(self):
        # Retorna un iterador personalizado para la clase Order
        return OrderIterator(self.items)


class OrderIterator:
    """Iterador para la clase Order."""
    def __init__(self, items):
        self._items = items
        self._index = 0

    def __next__(self):
        # Método que devuelve el siguiente elemento de la lista
        if self._index < len(self._items):
            item = self._items[self._index]
            self._index += 1
            return item
        else:
            raise StopIteration

    def __iter__(self):
        # Devuelve el iterador mismo
        return self

    def done(self):
        """Devuelve True si no hay más elementos por iterar."""
        return self._index >= len(self._items)


class PayMethod:
    def __init__(self):
        pass

    def pay(self, amount):
        return amount, "Successfully paid"


class Card(PayMethod):
    def __init__(self, number, cvv, expiration_date):
        super().__init__()
        self.number = number
        self.cvv = cvv
        self.expiration_date = expiration_date


class Cash(PayMethod):
    def __init__(self):
        super().__init__()


# Ejemplo de uso
order = Order()

jugo = Beverage(name="Jugo", price=4000, size="22 oz")
total_price_jugo = jugo.calculate_total_price(10)
order.add_item(jugo)

mazorca = Appetizer("Mazorca", 3500, "grande")
mazorca.calculate_total_price(3)
order.add_item(mazorca)

mignon = MainCourse("Fillet mignon", 34600, "grande", meat=True)
mignon.calculate_total_price(2)

item1 = Beverage("Soda", 2000, "12 oz")
item2 = Appetizer("Nachos", 5000, "Regular")
item3 = MainCourse("Spaghetti", 12000, "Large", meat=False)
item4 = Beverage("Iced Tea", 1500, "16 oz")
item5 = Appetizer("Chicken Wings", 8000, "Large")
item6 = MainCourse("Grilled Salmon", 18000, "Medium", meat=True)
item7 = Beverage("Lemonade", 2500, "20 oz")

order.add_item(mignon)
order.add_item(item1)
order.add_item(item2)
order.add_item(item3)
order.add_item(item4)
order.add_item(item5)
order.add_item(item6)
order.add_item(item7)

# Uso del iterador de forma explícita
iterator = iter(order)  # Se obtiene el iterador de la orden
while not iterator.done():  # Se usa el método done() para verificar si hay más elementos
    item = next(iterator)  # Se obtiene el siguiente elemento con next()
    print(f"Item: {item.get_name()}, Price: {item.get_price()}, Size: {item.get_size()}")

# Mostrar items con precios usando la función
items_with_prices = order.get_items_with_prices()
print("\nItems with each price:\n", items_with_prices)

# Calcular la factura total
total_bill = order.calculate_total_bill()
print("\nTotal bill amount:", total_bill)

# Aplicar descuento
discount = order.discount(90)
print("Discounted bill amount:", discount)

# Pago
payment1 = Card(123456789, 123, "12/23")
payment_amount, payment_status = payment1.pay(discount)
print(f"Payment amount: {payment_amount}, Status: {payment_status}")


```

For the creation of diagram 

``` mermaid
classDiagram
    class MenuItem {
        -name: str
        -price: float
        -size: str
        +get_name(): str
        +set_name(value: str): void
        +get_price(): float
        +set_price(value: float): void
        +get_size(): str
        +set_size(value: str): void
        +calculate_total_price(quantity: int): float
    }

    class Beverage {
        +Beverage(name: str, price: float, size: str)
    }

    class Appetizer {
        +Appetizer(name: str, price: float, size: str)
    }

    class MainCourse {
        -meat: bool
        +MainCourse(name: str, price: float, size: str, meat: bool)
        +get_meat(): bool
        +set_meat(value: bool): void
        +calculate_total_price(quantity: int): float
    }

    class Order {
        -items: list
        +add_item(item: MenuItem): void
        +calculate_total_bill(): float
        +discount(amount: float): float
        +get_items_with_prices(): str
        +__iter__(): OrderIterator
    }

    class OrderIterator {
        -items: list
        -index: int
        +__next__(): MenuItem
        +__iter__(): OrderIterator
        +done(): bool
    }

    class PayMethod {
        +pay(amount: float): tuple
    }

    class Card {
        -number: int
        -cvv: int
        -expiration_date: str
        +Card(number: int, cvv: int, expiration_date: str)
    }

    class Cash {
        +Cash()
    }

    MenuItem <|-- Beverage
    MenuItem <|-- Appetizer
    MenuItem <|-- MainCourse
    Order o-- MenuItem
    Order --> OrderIterator
    PayMethod <|-- Card
    PayMethod <|-- Cash


```