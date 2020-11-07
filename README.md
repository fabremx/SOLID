# SOLID principles

### S: Single Responsibility Principle
>"A class or function should only have one reason to change."

###### BAD

```js
class Client {
    name: string;
    balance: number;

    getNameAndBalanceValue(): string {
        return `${this.name} : ${this.balance} euros`
    }

    validBalanceAndMakePayment(payment: any): void {
        if (this.balance > 0) {
            fetch('make payment')
                .then((success) => success)
                .catch(error => error)
        }
    }
}
```

###### GOOD

```js
class Client {
    name: string;
    balance: number;

    getName(): string {
        return this.name
    }

    getBalance(): number {
        return this.balance
    }

    isBalancePositif(): boolean {
        return this.balance > 0
    }

    sendPayment(payment: any): void {
        fetch('make payment')
                .then((success) => success)
                .catch(error => error)
    }
}
```
