# General JSONRPC External Adapter for Chainlink

- Should work for any JSON RPC supported endpoint (includes tests for a few major projects which support JSON RPC commands)
- Supports AWS Lambda and GCP Functions
- Blockchain clients can sign and send transactions if wallet is unlocked
- Takes optional connection to RPC endpoint (set via `RPC_URL` environment variable)

A JSON-RPC request is typically formatted like this:

```JSON
{
	"jsonrpc": "2.0",
	"method": "some_method",
	"params": [
		"some_param",
		"another_param"
	],
	"id": 1
}
```

What this adapter does is allow you to specify the `"method"` and `"params"` values in a Chainlink request and receive the result (format example below) back to the Chainlink node for further processing.

```JSON
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "some_result"
}
```

In Solidity, a Chainlink request can be made to an external chain for the balance of a given account with the following example:

```javascript
function getBalanceExternalChain(string _account)
  public
  onlyOwner
{
  Chainlink.Request memory req = newRequest(JOB_ID, this, this.fulfillRPCCall.selector);
  req.add("method", "eth_getBalance");
  string[] memory params = new string[](2);
  path[0] = _account;
  path[1] = "latest";
  req.addStringArray("params", params);
  chainlinkRequest(req, ORACLE_PAYMENT);
}
```

## Install

Install dependencies

```bash
npm install
```

Create the zip

```bash
zip -r cl-jsonrpc.zip .
```

Upload to AWS/GCP

Set the `RPC_URL` environment variable to your client URL.

## Testing

Testing is dependent on the type of node you're connecting to. You can set a local environment variable `RPC_URL` to point to an RPC connection. Otherwise, the adapter will default to `"http://localhost:8545"`.

RPC Address and Port Defaults:
- Ethereum: http://localhost:8545
- AION: http://localhost:8545
- BTC: (bitcoind) http://localhost:8332 (btcd) http://localhost:8334
- Zilliqa: http://localhost:4201

For Ethereum and any Geth clone (should work with Parity as well):

```bash
npm run test:eth
```

For Bitcoin:

```bash
npm run test:btc
```

For AION:

```bash
npm run test:aion
```

For Zilliqa:

```bash
npm run test:zilliqa
```

## Install to GCP

- In Functions, create a new function, choose to ZIP upload
- Click Browse and select the `cl-jsonrpc.zip` file
- Select a Storage Bucket to keep the zip in
- Function to execute: gcpservice
- Click More, Add variable
  - NAME: `RPC_URL`
  - VALUE: `Replace_With_Something_Unique`

## Install to AWS Lambda

- In Lambda Functions, create function
- On the Create function page:
  - Give the function a name
  - Use Node.js 8.10 for the runtime
  - Choose an existing role or create a new one
  - Click Create Function
- Under Function code, select "Upload a .zip file" from the Code entry type drop-down
- Click Upload and select the `cl-jsonrpc.zip` file
- Handler should remain index.handler
- Add the environment variable:
  - Key: `RPC_URL`
  - Value: `Replace_With_Something_Unique`
- Save
