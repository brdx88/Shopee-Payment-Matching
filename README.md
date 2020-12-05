# Shopee Payment Matching - National Data Science Challenge 2020
At Shopee, bank transfer is a payment method in most countries. When a buyer chooses to place an order using bank transfer, he/she is supposed to make the transfer within 2 days after he/she places the order.

After he/she makes the transfer, Shopee will receive a bank statement from the bank and Shopee needs to compare and match the bank statement with the checkout information in order to confirm that this particular order has been paid. This process is called payment matching.

Two criteria need to be met in order to match a bank statement with a checkout:
- Amount match: Statement amount equals checkout amount.
- Name match: Statement description “matches” checkout buyer name (Note: statement description usually contains buyer name)

A proper match occurs when both the amount and the name matches on both bank statement and checkout list.

## About The Dataset
The dataset was separated into two csv files: bank statement and checkout description.
Bank Statement Dataset
![1](https://github.com/brdx88/Shopee-Payment-Matching/blob/main/images/1.png)

Checkout Statement Dataset
![1](https://github.com/brdx88/Shopee-Payment-Matching/blob/main/images/2.png)

## 1) Removing Unwanted Characters first

```python
bank['desc']        = bank['desc'].apply(lambda x: re.sub(r"[^a-zA-Z0-9]+", ' ', x))
check['buyer_name'] = check['buyer_name'].apply(lambda x: re.sub(r"[^a-zA-Z0-9]+", ' ', x))
bank['desc']        = bank['desc'].apply(lambda x: re.sub(r"TRANSFER", ' ', x))
check['buyer_name'] = check['buyer_name'].apply(lambda x: re.sub(r"TRANSFER", ' ', x))
```

## 2) Match the Price (amount) and the Buyer Name
![1](https://github.com/brdx88/Shopee-Payment-Matching/blob/main/images/3.png)

```python
prices = sorted(list(set(check['ckt_amount'])))
bs = bank.values.tolist()
co = check.values.tolist()

curr_bank, curr_trans, pend_bank, pend_trans = [], [], [], []
answer = []
no_ans_trans, no_ans_bank = [], []

for price in prices:
    while bs and bs[0][1] == price:
        curr_bank.append(bs.pop(0))
    while co and co[0][1] == price:
        curr_trans.append(co.pop(0))

    for trans in curr_trans:
        found = False
        curname = trans[2]
        bk = list(filter(lambda x: x[2].intersection(curname), curr_bank))
        if bk:
            b = max(bk, key= lambda x: len(x[2].intersection(curname)))
            answer.append((trans, b))
            curr_bank.remove(b)
        else:
            no_ans_trans.append(trans)
            
    no_ans_bank.extend(curr_bank)
            
    curr_bank, curr_trans = [], []

```

## 3) Transactions which not Matched (yet)
![1](https://github.com/brdx88/Shopee-Payment-Matching/blob/main/images/4.png)
![1](https://github.com/brdx88/Shopee-Payment-Matching/blob/main/images/5.png)

```python
prices_new = sorted(list(set(check_new['ckt_amount'])))
bs2, co2 = bank_new.values.tolist(), check_new.values.tolist()

curr_bank2, curr_trans2 = [], []
answer2 = []

for price in prices_new:
    while bs2 and bs2[0][1] == price:
        curr_bank2.append(bs2.pop(0))
    while co2 and co2[0][1] == price:
        curr_trans2.append(co2.pop(0))
    for trans in curr_trans2:
        curname = trans[2]
        b = max(curr_bank2, key= lambda x: fuzz.partial_ratio(curname, x[2]))
        answer2.append((trans, b))
        curr_bank2.remove(b)
    curr_bank2, curr_trans2 = [], []

```

## 4) Merge Those Two Transactions and Show `The Bank Statement ID and Checkout Statement ID`
At the end
![1](https://github.com/brdx88/Shopee-Payment-Matching/blob/main/images/6.png)
