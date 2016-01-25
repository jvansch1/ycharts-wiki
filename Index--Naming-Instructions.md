## Index Naming Instructions

These are some notes in how to create official YCharts names and symbols for indices.

You will be starting with a file called all_index_list.xls in directory apps/indices/onetime_scripts/index_lists

Read the file in.

For each row, you will be filling out the following columns:

* YCharts Name
* YCharts Desired Ticker
* Xignite New Ticker
* Xignite Legacy Ticker
* YCharts Final Ticker

## YCharts Name
This is the name of index in the YCharts system.
The name will consist of 4 possible data items

* Normalized Name
* Return Type
* Currency
* Hedging

Follow the following formula:

1. Start with the Normalized Name
2. If the return is not "Price Return" or "Synthetic Price Return" then add that to the name
3. If the currency is not the indexes "home currency," add the currency code to the name. The "home currency" will be defined in the index provider specific section of this document.
4. If the currency is hedged add the currency and the word "Hedged" to the name. Note that if the currency was not included in part 3, then add the currency and then add the word hedged

## YCharts Desired Ticker

The YCharts Symbol starts with a caret "^" and can include up to 10 other characters.
The basic structure of the symbol is:
^ + Family Prefix + Core Name + Return Type + Currency + Hedging

The pieces of the symbol will be detailed below.

### Family Prefix
 The Prefix represents part of the normalized name that's pulled out and represented uniformly so there is no mistaking where the index is from.

Below are the  family of indices we have seen/ support thus far:

Family | Prefix | Prefix Pull Out of Normalized Name
------------ | ------------- | ---------------
CBOE | CB | CBOE
Dow Jones | DJ | Dow Jones
S&P | SP | S&P 
S&P Promotional | Defined on a case by case basis, see notes below | Ditto
NASDAQ | NA | NASDAQ
NYSE | NY | NYSE
Russell | RU | Russell
TSX | TS | S&P/TSX
Barclays | BB (got bought by Bloomberg, thus the BB) | Barclays
MSCI | MS | MSCI

Note, that there might be special cases to the prefixes above that will be noted in the provider specific section of this document.

### Core Name
Here is the basic algo to follow:

1. Start with the normalized name, and take out the part of the name that is already represented in the prefix.
2. Take out the words: "Index", "The"
3. Filter the name to all capital letter and numbers

Note, that there will be special cases to the name algo above that will be noted in the provider specific section of this document.

### Return Type
Follow this mapping:

Return Type | Abbreviation
------------ | -------------
Price Return |
Synthetic Price Return |
Total Return | TR
Net Total Return | NTR
Notional Net Total Return | OTR
Excess Return | ER

However, if the index symbol thus far is > 5 characters, uses this shortened mapping:

Return Type | Abbreviation
------------| -------------
Price Return |
Synthetic Price Return |
Total Return | T
Net Total Return | N
Notional Net Total Return | O
Excess Return | E

### Currency

If the currency is not the indexes "home currency," add the currency code to the name.

If the currency code addition makes the index too long, us a shorten version of the currency:

We started a few so you get an idea, you can make up your own

Currency Code | Shortened Currency Code
------------- | -----------------------
USD | U
EUR | E
CAD | C
JPY | J
AUD | A
CHF | S
GBP | G
CNY | Y
TWD | TD
HKD | HD
BRL | RR
SGD | SD
DKK | DK
KRW | WO
NOK | NK
NZD | ND
SEK | SK
PLN | PL
CNH | CH
ILS | IS
THB | TB
MXN | MP

### Hedging

Simply add an H to the symbol. If this makes the symbol too long and earlier you used the full currency code in the symbol, switch to using the shorter currency code.

## Xignite New Ticker

## Xignite Legacy Ticker

## YCharts Final Ticker




## Provider Specific Notes

#### Provider

##### Home Currency
Note

##### Prefix
Note

##### Core Name
Note

#####  Xignite New Ticker Matching
Note