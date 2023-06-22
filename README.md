# gmx-handbook
GMX Decentralized Perpetual Exchange - Code Samples 

GMX Trades and Orders monitoring is available in my bot: https://t.me/GMXTraderBot

# Get positions from GMX Positions Reader

Smart contract: https://arbiscan.io/address/0x22199a49A999c351eF7927602CFB187ec3cae489

```
addrUSDC = "0xFF970A61A04b1cA14834A43f5dE4533eBDDB5CC8"
addrUSDT = "0xFd086bC7CD5C481DCC9C85ebE478A1C0b69FCbb9"
addrDAI = "0xDA10009cBd5D07dd0CeCc66161FC93D7c9000da1"

addrETH = "0x82aF49447D8a07e3bd95BD0d56f35241523fBab1"
addrBTC = "0x2f2a2543B76A4166549F7aaB2e75Bef0aefC5B0f"
addrUNI = "0xFa7F8980b0f1E64A2062791cc3b0871572f1F7f0"
addrLINK = "0xf97f4df75117a78c1A5a0DBb814Af92458539FB4"
addrFRAX = "0x17FC002b466eEc40DaE837Fc4bE5c67993ddBd6F"

collateralTokens = [addrUSDC, addrUSDT, addrDAI, addrETH, addrBTC, addrUNI, addrLINK, addrFRAX]

print(collateralTokens)

indexTokens = [indexTkn, indexTkn, indexTkn, indexTkn, indexTkn, indexTkn, indexTkn, indexTkn]

isLong = [isLong, isLong, isLong, isLong, isLong, isLong, isLong, isLong]

# If you want to check other combinations just add the new addresses and true/false for L/S

with open('abi/gmx_arb_reader.abi', 'r') as f:
    gmx_arb_reader_abi = f.read()

gmx_arb_reader_contract = "0x22199a49A999c351eF7927602CFB187ec3cae489"

web3 = Web3(Web3.HTTPProvider(https://endpoints.omniatech.io/v1/arbitrum/one/public))

contract_reader = web3.eth.contract(address=Web3.toChecksumAddress(gmx_arb_reader_contract), abi=gmx_arb_reader_abi)

# arbitrum vault

vault_addr = "0x489ee077994B6658eAfA855C308275EAd8097C4A"

sampleCall = contract_reader.functions.getPositions(vault_addr, gmx_account, collateralTokens, indexTokens, isLong).call()

```
A list of position details can be retrieved by calling Reader.getPositions
- _vault: the vault contract address 
- _account: the account of the user
- _collateralTokens: an array of collateralTokens
- _indexTokens: an array of indexTokens
 -_isLong: an array of whether the position is a long position

The returned positions will be in the order of the query, for example, given the following inputs:
- _collateralTokens: [WBTC.address, WETH.address, USDC.address] 
- _indexTokens: [WBTC.address, WETH.address, WBTC.address]
- _isLong: [true, true, false]

The position details would be returned for
- Long BTC position, positionIndex: 0
- Long ETH position, positionIndex: 1
- Short BTC position, positionIndex: 2

More details here:
https://gmxio.gitbook.io/gmx/contracts#positions-list

# Get limit orders from  GMX Order Reader

Smart contract: https://arbiscan.io/address/0xa27c20a7cf0e1c68c0460706bb674f98f362bc21#code

Input: order_index


```
with open('abi/gmx_arb_orders_reader.abi', 'r') as f:
    gmx_arb_reader_abi = f.read()

gmx_arb_reader_contract = Web3.toChecksumAddress("0xa27C20A7CF0e1C68C0460706bB674f98F362Bc21")

web3 = Web3(Web3.HTTPProvider(INFURA_URL))

contract_reader = web3.eth.contract(address=Web3.toChecksumAddress(gmx_arb_reader_contract), abi=gmx_arb_reader_abi)

orderBookAddress = Web3.toChecksumAddress("0x09f77e8a13de9a35a7231028187e9fd5db8a2acb")

if isIncrease == True:
    sampleCall = contract_reader.functions.getIncreaseOrders(orderBookAddress, Web3.toChecksumAddress(gmx_account), [orderIndex]).call()

else:
    sampleCall = contract_reader.functions.getDecreaseOrders(orderBookAddress, Web3.toChecksumAddress(gmx_account), [orderIndex]).call()

```

Output sample:

```
[[300000000000000000, 10064341272921810699420000000000000, 1, 1580000000000000000000000000000000, 0], 
['0x82aF49447D8a07e3bd95BD0d56f35241523fBab1', '0x82aF49447D8a07e3bd95BD0d56f35241523fBab1', '0x82aF49447D8a07e3bd95BD0d56f35241523fBab1']]

```
First array:
- _amountIn: the amount of tokenIn you want to deposit as collateral
- _sizeDelta: the USD value of the change in position size
- bool isLong
- uint256 triggerPrice
- bool triggerAboveThreshold,

Second array:
- address purchaseToken, 
- address collateralToken,
- address indexToken

# Get list of existing Limit Orders Indexes

We can write something like this:

```
api_endpoint = f"https://gmx-server-mainnet.uw.r.appspot.com/orders_indices?account={Web3.toChecksumAddress(gmx_account)}"

payload = {}
headers = {}

response = requests.request("GET", api_endpoint, headers=headers, data=payload)

try:
    response = requests.request("GET", api_endpoint, headers=headers, data=payload)
    response.raise_for_status()
except requests.exceptions.HTTPError as errh:
    print("Http Error:",errh)
except requests.exceptions.ConnectionError as errc:
    print("Error Connecting:",errc)
except requests.exceptions.Timeout as errt:
    print("Timeout Error:",errt)
except requests.exceptions.RequestException as err:
    print("OOps: Something Else",err)

try:
    list_of_orders = json.loads(response.text)
except ValueError as e:
    return json.loads("[]")

return list_of_orders
```



