# How to use L2 Auth

## Example

```typescript
import { ethers } from "ethers";
import { Wallet } from "@ethersproject/wallet";
import { JsonRpcSigner } from "@ethersproject/providers";
import { config as dotenvConfig } from "dotenv";
import { resolve } from "path";
import { ApiKeyCreds, Chain, createL2Headers } from "@polymarket/clob-client";
import axios from "axios";

dotenvConfig({ path: resolve(__dirname, ".env") });

(async () => {
  const wallet = new ethers.Wallet(`${process.env.PK}`);
  const chainId = parseInt(`${process.env.CHAIN_ID || Chain.MUMBAI}`) as Chain;
  console.log(`Address: ${await wallet.getAddress()}, chainId: ${chainId}`);

  const host = process.env.CLOB_API_URL || "https://clob.polymarket.com";
  const creds: ApiKeyCreds = {
    key: `${process.env.CLOB_API_KEY}`,
    secret: `${process.env.CLOB_SECRET}`,
    passphrase: `${process.env.CLOB_PASS_PHRASE}`,
  };

  const endpoint = "/rewards/user";
  const headerArgs = {
    method: "GET",
    requestPath: endpoint,
  };

  const headers = await createL2Headers(
    wallet as Wallet | JsonRpcSigner,
    creds as ApiKeyCreds,
    headerArgs
  );

  const params = {
    date: "2024-04-10",
    signature_type: 1,
    next_cursor: "",
  };

  const response = await axios({
    method: "GET",
    url: host + endpoint,
    headers,
    params,
  });

  console.log(response.data);
})();
```

## Try it

- Create a `.env` file considering `.env.example`
- Execute `npx ts-node app.ts`
