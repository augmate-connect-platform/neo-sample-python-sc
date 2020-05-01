# a simple Python Smart Contract sample

This is a simple Python smart contract sample that is mainly to *show the steps to compile. deploy and test a Python smart contract on a Neo private network on Morpheus Labs Blockchain Platform as a Service (ML BPaaS)*.

If you are not familiar with the ML BPaaS yet, you may refer to "https://docs.morpheuslabs.io/docs/overview" understand the basic concept and the operations required:

- create a new Neo private network in "Blockchain Ops - Neo"
- download apps using Application Library
- Manage workspace in CDE

If you want to study more anout Neo blockchain and neo-python, you may refer to https://docs.neo.org/en-us/index.html and  https://github.com/CityOfZion/neo-python (you can bypass this step to run the sample python smart contract first)

## Setup Environment
---------------------------------
1. Create a Neo private network on "Blockchain Ops"
2. Download "Try Neo Python Smart Contract" from Application Library into My Repository
3. Create a new workspace using the newly downloaded application in My Repository
4. Start and open the newly created workspace in CDE
5. Connect neo-python client in CDE to the private network

In the terminal, use the command "np-config {internal P2P URL} {internal RPC URL}" to connect neo-python to the private network

For example:

    `np-config http://bops-t.morpheuslabs.io:21660 http://bops-t.morpheuslabs.io:33362`

Please note that if you restart the workspace, you have to run the command again. Or if you are using a new private blockchain, then you have to run the command with the internal P2P URL and the RPC URL of the new private network.

You can get the internal P2P URL and internal RPC URL from the information tab of of the private netowork in Blockchain Ops as seen in the example below:

- `Internal RPC URL: http://bops-t.morpheuslabs.io:33362`
- `Internal P2P URL: http://bops-t.morpheuslabs.io:21660`

For information, the command above will update "SeedList" and "RPCList" in the file

`/home/user/.local/lib/python3.6/site-packages/neo/data/protocol.privnet.json`

For example:

```
"SeedList": [
    "bops-t.morpheuslabs.io:21660" //using Internal P2P URL without http
],
"RPCList": [
    "http://bops-t.morpheuslabs.io:33362" //using Internal RPC URL with full link
]
```

6. Connect to the private network

In the terminal,  enter command `np-prompt -p` to connnect to the private network

You should see something like this:

```
Privatenet useragent '/Neo:2.10.1/', nonce: 977855951
[I 190616 15:25:54 LevelDBBlockchain:112] Created Blockchain DB at /home/user/.neopython/Chains/privnet
[I 190616 15:25:55 NotificationDB:73] Created Notification DB At /home/user/.neopython/Chains/privnet_notif
NEO cli. Type 'help' to get started


neo>
```

It means that we have connected to the private network. Wait for a while for the neo-python client in the terminal to sync the blocks from the private network. You can check synching status by using the command 

`show state`

You will see some info like this showing the status of syncing. In the example below, 100 blocks have been synched from the private network to the neo-python client node.
```
neo> show state
Progress: 100 / 4588
Block-cache length 1
Blocks since program start 100
Time elapsed 0.43487291666666666 mins
Blocks per min 229.9522370041056
TPS: 4.100814893239884
```

7. Create a new wallet or open an existing wallet

Enter `neo> wallet create ./mywallet` and fill in a password to create a new wallet. Or open an existing wallet `neo> wallet open {path to wallet file}`

8. Import the default account of the private network to the wallet 

`neo> wallet import wif KxDgvEKzgSBPPfuVfw67oPQBSjidEiqTHURKSDL1R7yGaGYAeYnr`

This default account has NEO and GAS tokens for you to perform various transactions on the private network.

9. Rebuild the wallet after import 

`neo> wallet rebuild`

10. Check NEO and GAS in the wallet 

`neo> wallet` 

Check `percent_synced` and `synced_balances` to confirm there are NEO and GAS tokens in the wallet.

```
    "percent_synced": 100,
    "synced_balances": [
        "[NEO]: 100000000.0 ",
        "[NEOGas]: 30525.98755 "
    ],
```
The wallet needs to have NEO and GAS to be able to deploy the smart contract.

Next, we will compile the smart contract, deploy and test the smart contract using the private network.

# Compile & Deploy & Test the smart contract
--------------------------------------------

1. Compile and build the smart contract 

use the command "neo> sc build {path to python file}", for example:

`neo> sc build /projects/neo-sample-python-sc/smart-contract/sample.py`

2. Deploy the smart contract 

Enter the command "neo>sc deploy {path to .avm file} False False False 070202 02"

Then enter the details of the contract information and provide the password of the wallet to complete the deployment. 

The example of the command: 

`neo> sc deploy /projects/neo-sample-python-sc/smart-contract/sample.avm False False False 070202 02`

3. Get the script hash of the smart contract

Wait for the deployment to finish and view the detail of the smart contract to get the script hash of the contract address which is usually used by the client application to involke or call the smart contract functions.

Example of the details of the smart contract:
```
Creating smart contract....
                 Name: Sample
              Version: V1
               Author: Neo
                Email: neo@test.com
          Description: Sample App
        Needs Storage: False
 Needs Dynamic Invoke: False
           Is Payable: False
{
    "hash": "0xb6730fd741b632401f89020409c6c0415d97dcee", //script hash of the smart contract
    "script": "011ac56b6a00527ac46a51527ac4586a52527ac4074e4744205045546a53527ac4034e50546a54527ac46a00c30b746f74616c537570706c79876406006c7566616a00c3046e616d65876409006a53c36c7566616a00c30673796d626f6c8764...
```
4. Test the smart contract

Invoke the smart contract function using the command "sc invoke {script_hash} {paramters}"

Example (please replace the script hash in the command with the hash in your smart contract):
```
neo> sc invoke 0xb6730fd741b632401f89020409c6c0415d97dcee add 02 03
```

Below is an example of the test result:

```
Test invoke successful
Total operations: 39
Results [{'type': 'Integer', 'value': '5'}]
Invoke TX GAS cost: 0.0
Invoke TX fee: 0.0001
```
You can see the result in the results field. 

It will prompt to enter the wallet password, you can enter an incorrect password if you do not want to submit the transaction to the private network.

You can use the IDE to open and explore the smart contract function. You can change the smart contract using CDE.

The original python smart contract:

```
def Main(operation, a, b):

    if operation == 'add':
        return a + b

    elif operation == 'sub':
        return a - b

    elif operation == 'mul':
        return a * b

    elif operation == 'div':
        return a / b

    else:
        return -1
```

You can make minor changes to `return a + b + 1` and then save the change in IDE.

Then test the changes following the steps in "Compile & Deploy & Test the smart contract".

You can see the retuen value has been changed to "6".

```
Results [{'type': 'Integer', 'value': '6'}]
```
