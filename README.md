# SOLID principles

## Table of Contents
1. [S: Single Responsibility Principle](#S)
2. [O: Open-Closed Principle](#O)
3. [L: Liskov-Substitution Principle](#L)
4. [I: I: Interface Segregation Principle](#I)
4. [D: Dependency Inversion Principle](#D)

### S: Single Responsibility Principle <a name="S"></a>
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

✅ ###### GOOD

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

### O: Open-Closed Principle <a name="O"></a>
>"A software artifact should be open for extension but closed for modification."

Below a service able to make a trasaction through an external API

```js
class TransactionService {
  plateforme: any

  constructor (plateformeAInstance) {
    this.plateforme = plateformeAInstance;
  }

  sendTransaction (from, to, value): void {
    // Implementation
  }
}
```

We want to add new plateforms in order to make transaction with Traders ...

###### BAD

```js
 class Trader {
  transactionAService = new TransactionAService()
  transactionBService = new TransactionBService()
  
  sendTransaction(transaction, plateformeType): void {
    switch (plateformeType) {
        case 'A':
            this.transactionAService.sendMail(transaction);
        case 'B':
            this.transactionBService.sendMail(transaction);
    } 
  }
}

// OR

class SendTransactionService {
    plateforme: any

    constructor (plateformeInstance) {
        this.plateforme = plateformeInstance;
    }

    sendTransaction (from, to, value): void {
        switch (this.plateforme.type) {
            case 'A':
                // sendThroughPlatformA
            case 'B':
                // sendThroughPlatformB
        }
    }
}
```

###### GOOD

```js
interface IPlateformService {
    sendTransaction(transaction: any): void
  }
  
  class SendPlateformAService implements IPlateformService {
    sendTransaction(transaction: any): void {}
  }
  class SendPlateformBService implements IPlateformService {
    sendTransaction(transaction: any): void {}
  }

  class Trader {
    private plateformeService: IPlateformService;

    constructor (plateformeService: IPlateformService) {
        this.plateformeService = plateformeService;
    }

    sendTransaction(transaction): void {
        this.plateformeService.sendTransaction(transaction);
    }
}
```

### L: Liskov-Substitution Principle <a name="L"></a>
>"To build software systems from interchangeable parts, those parts must adhere to a contract that allows those parts to be substituted one for another."

###### GOOD

```js
interface IPlateformService {
    sendTransaction(transaction: any): void
}

class PlateformAService implements IPlateformService {
    sendTransaction(transaction: any): void {
        // Implementation
    }
}
class PlateformBService implements IPlateformService {
    sendTransaction(transaction: any): void {
        // Implementation
    }
}

class Trader {
    private plateformeService: IPlateformService;

    constructor (plateformeService: IPlateformService) {
        this.plateformeService = plateformeService;
    }

    sendTransaction(transaction): void {
        this.plateformeService.sendTransaction(transaction);
    }
}

const plateformAService = new PlateformAService();
const plateformBService = new PlateformBService();

// any of these are valid 
const trader = new Trader(plateformAService);
const trader = new Trader(plateformBService);
```

### I: Interface Segregation Principle <a name="I"></a>
>"Prevent classes from relying on things that they dont need."

###### BAD

```js
interface Employee {
    makeTransaction();
    allowBankLoan();
}

class Trader implements Employee {
    makeTransaction();
    allowBankLoan();
}
class BankEmployee implements Employee {
    makeTransaction();
    allowBankLoan();
}
```

###### GOOD

```js
interface TraderEmployee {
    makeTransaction();
}
interface BankEmployee {
    allowBankLoan();
}
interface CaptainAmericaWeapon {
    Sheild();
}

class Trader implements TraderEmployee {
    makeTransaction() { 
        // Implementation
    }
}
```

### D: Dependency Inversion Principle <a name="D"></a>
>"Abstractions should not depend on details. Details should depend on abstractions."

###### BAD

```js
class User {
  private emailService: SendGridEmailService;

  constructor (emailService: SendGridEmailService) {
    this.emailService = emailService;
  }

  protected sendMail (): void {
    const mail = new Mail()
    this.emailService.sendMail(mail);
  }
}

// OR

import { SendGridService } from '../../services'; // <- source code dependency

class CreateUserController {
  private emailService: SendGridEmailService = new SendGridEmailService();

  protected sendMail(): void {
    const mail = new Mail()
    this.emailService.sendMail(mail);
  }
}
```

###### GOOD

```js
interface IMailService {
    sendMail(email: Email): Promise<EmailTransmissionResult>
}


class SendGridEmailService implements IMailService {
    sendMail(email: Email): Promise<EmailTransmissionResult> {
        // Implementation
    }
}

class User {
    private emailService: IEmailService;

    constructor (emailService: IEmailService) {
        this.emailService = emailService;
    }

    protected sendMail (): void {
        const mail = new Mail()
        this.emailService.sendMail(mail);
    }
}
```
