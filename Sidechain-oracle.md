# LX Bridge Service

## Intro

Основные две задачи которые призван решить LX Bridge Service:
1. Синхронизация deposit балансов
2. Вывод намайненых ETH-LX в main net (обмен на LHT)
3. Обмен ETH-LX (sidechain) на LHT(mainnet) и наоборот

## Синхронизация deposit балансов*

Идея в том что бы постоянно синхронизировать величину депозитов в mainnet cо значениями в lx-sidechain. Это необходимо для расчета вознагражения за майнинг блока.

В этому процессе участвуют 3 компонента:
- TimeHolder (mainnet)
- LXTimeHolder (lx-sidechain)
- oracle

### TimeHolder (Mainnet / SmartContracts)

События `TimeHolder` контракта которые эмитятся во время изменения баланса deposit в `mainnet`:

````
- event Deposit(address token, address who, uint amount); (увеличивает deposit)
- event WithdrawShares(address token, address who, uint amount, address receiver); (уменьщает deposit)
- event FeatureFeeTaken(address self, address indexed from, address indexed to, uint amount); (уменьщает deposit TIME)
````

### LXTimeHolder (lx-sidechain / LX SmartContracts)

`LX Bridge Service` должен быть добавден в качестве администратора `LXTimeHolder`, и Во время изменения депозитов в mainnet, необходимо выполнять следующие функции `LXTimeHolde`r контракта

```
function increaseDeposit(address depositHolder, address token, uint amount);
function decreaseDeposit(address depositHolder, address token, uint amount);
```

В случае` FeatureFeeTaken` необходимо использовать адрес `TIME`.

### Workflow

`LX Bridge Service` ловит ивенты в` mainnet`, выполняет соответствующий метод `LXTimeHolder` в `lx-sidechain`.
Все данные должны быть переданы без изменений.

## Вывод намайненых ETH-LX в mainnet (обмен на LHT)

Одним из ключевых экономических стимулов поддержания `lx-sidechain` (участие в майнинге) является возможность обмена намайненных `ETH-LX` с сети `lx-sidechain` на LHT в `mainnet`.

### LXExhangeGate

```
contract LXExhangeGate  {
    event LXETHReceived(address sender, uint amount);

    modifier onlyAuthorised() {
        require(isMiner(msg.sender));
    }

    function () onlyAuthorised payable {
        LXETHReceived(msg.sender, msg.value);
    }
    ….
}
```

### Workflow

- Словить событие `LXETHReceived` в `lx-sidechain`
- Заминтить `LHT` в `mainnet` для полученного `sender`

Topic to discuss :
- `LX Bridge Service` должен быть part owner в `LHT`.
- Манеры не должны иметь возможность заминтить больше чем намайнили, потому `LX Bridge Service` должен считать кол-во полученных ревордов за замайненные блоки.

## Обмен ETH-LX (sidechain) на LHT(mainnet) и наоборот

Пользователи сети `lx-sideсhain` должны иметь возможность получить `ETH-LX` (основное платежное средство в сети lx-sidechain) для возможности работы в `lx-sidechain` и вывести его в mainnet обменяв на `LHT`.

Отличительной особенностью является, то что в этом проессе не генирируются новые токены LHT. Во время обмена их чисто не меняется.

Для этого предлагается заимплеменить двухсторонний `channel` состоящий для 2х гейтов и оракла связывающего их:
- ExchangeLXGate.sol
- ExchangeMainnetGate.sol
- oracle

### Workflow

#### Покупка ETHLX
- пользователь в mainnet ‘отправляет’ (approve) LTH на адресс ExchangeLXGate.sol и выполняет function buyETHLX(uint amount, uint rate)
- эмитится event ExchangeBuy(address indexed exchange, address indexed who, uint token, uint eth)
- оракул проверяет имеет ли возможность ExchangeMainnetGate.sol выполнить начисление ETHLX, и если да - производит перевод

#### Продажа ETHLX
- пользователь в `mainnet` ‘отправляет’ (approve) LTH на адресс ExchangeLXGate.sol и выполняет `function сellETHLX(uint amount, uint rate) paybale`, оплачивая работу oracle
- эмитится event `ExchangeCell(address indexed exchange, address indexed who, uint token, uint eth)`
- оракул проверяет имеет ли возможность `ExchangeMainnetGate.sol` выполнить списание `ETHLX`, и если да - производит начисление `LHT`

## Aliasses
 Для того что бы участники сети (майнеры) не использовали свои `private keys` для `mainnet`, будет добавлена возможность (через контракт в `lx-sidechain`) установки alias для оригинального адреса, который позволит делегировать способность манить блоки, аккаунту не имеющего реальные депозиты в `mainnet` (edited)
