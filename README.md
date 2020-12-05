# Shopee Payment Matching - National Data Science Challenge 2020
At Shopee, bank transfer is a payment method in most countries. When a buyer chooses to place an order using bank transfer, he/she is supposed to make the transfer within 2 days after he/she places the order.

After he/she makes the transfer, Shopee will receive a bank statement from the bank and Shopee needs to compare and match the bank statement with the checkout information in order to confirm that this particular order has been paid. This process is called payment matching.

Two criteria need to be met in order to match a bank statement with a checkout:
- Amount match: Statement amount equals checkout amount.
- Name match: Statement description “matches” checkout buyer name (Note: statement description usually contains buyer name)

A proper match occurs when both the amount and the name matches on both bank statement and checkout list.

# About The Dataset
The dataset was separated into two csv files: bank statement and checkout description.

## 1) Removing Unwanted Characters first

'''python
bank['desc']        = bank['desc'].apply(lambda x: re.sub(r"[^a-zA-Z0-9]+", ' ', x))
check['buyer_name'] = check['buyer_name'].apply(lambda x: re.sub(r"[^a-zA-Z0-9]+", ' ', x))
bank['desc']        = bank['desc'].apply(lambda x: re.sub(r"TRANSFER", ' ', x))
check['buyer_name'] = check['buyer_name'].apply(lambda x: re.sub(r"TRANSFER", ' ', x))
'''

## 2) Match the Price (amount) and the Buyer Name

## 3) Transactions which not Matched (yet)

## 4) Merge Those Two Transactions and Show `The Bank Statement ID and Checkout Statement ID`
