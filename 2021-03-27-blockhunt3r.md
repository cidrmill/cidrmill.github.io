---
layout: article
title: Busting Criminals Hiding in Blockchains
tags:
  - HackTheBox
  - Walkthrough
  - OSINT
  - Intelligence
share: "true"
---

# The Challenge

Blockchain data can't be deleted, and bad actors are using it to store blobs, we want to hunt down the user that has been uploading illegal pictures on the Ethereum Goerli testnet blockchain. We need to find one picture to use it as evidence to take him down. We know he was actively uploading pictures between 2020-07-30 and 2020-08-01.

# The Solution
We know that the data's stored on the Ethereum (ETH) Goerli testnet. Having never worked with Blockchain technology, or done intelligence work on it, I went straight to Google for answers.

After some searching I discovered etherscan.io, an ETH blockchain explorer, which had a portion of the site dedicated to the Goerli testnet (goerli.etherscan.io).

This allows people to search blocks and transactions, but there wasn't a way to search for transactions within a date range. Luckily Etherscan offered a public API that I used to accomplish this.

> Get Block Number by Timestamp
>
> [Parameters]
> timestamp format: Unix timestamp (supports Unix timestamps in seconds), closest value: 'before' or 'after'
>
> `https://api-goerli.etherscan.io/api?module=block&action=getblocknobytime&timestamp=1578638524&closest=before&apikey=YourApiKeyToken`

*Tip: You can convert a datetime string to a Unix epoch using `date`*

```sh
$ date -d '2020-07-30 00:00:00' +%s
1596092400

$ date -d '2020-08-01 00:00:00' +%s
1596265200
```

So I modified the parameter values and sent requests using `curl`.

```sh
$ curl "https://api-goerli.etherscan.io/api?module=block&action=getblocknobytime&timestamp=1596092400&closest=after&apikey=..."
{"status":"1","message":"OK","result":"3135730"}

$ curl "https://api-goerli.etherscan.io/api?module=block&action=getblocknobytime&timestamp=1596265200&closest=before&apikey=..."
{"status":"1","message":"OK","result":"3147248"}
```

I now had 2 different block numbers (**3135730** and **3147248**) and had to search every blocks between the two.

My initial thought was to write a script in Python or JavaScript and make calls to the Etherscan API that way, but I figured that wasn't the most efficient and would create lots of unnecessary load on Etherscan's infrastructure. I put that idea on the backburner and started exploring other options.

## Image Signatures

I took a random PNG (and other file types) I found on my computer and piped it to `xxd -p` to print the first 8 bytes, which is the signature for all PNG image files.

```sh
$ cat image.png | xxd -p | head
89504e470d.......eb9f02cc8372
```

I used this signature to tell the script, which I'll write later, how to find the image among all the data.

## Geth Console

After a Google search or two I came across the Github repository for the official Go-Lang implementation of the Ethereum protocol, [Geth](https://github.com/ethereum/go-ethereum).

### Installation

```sh
$ git clone https://github.com/ethereum/go-ethereum ./geth
$ cd geth
$ make geth
```

Running geth with a few arguments will start downloading the full Goerli blockchain and allow the program to run in console mode. The full download may take an hour or two, but you only need part of it.

```sh
$ geth --goerli console 2>/dev/null
```

`--goerli` tells Geth to use the test blockchain "Goerli".

`console` tells Geth to open an interactive JavaScript console.

`2>/dev/null` will suppress output other than that of the console.

Once the download inches close to completion we can interact with the needed parts of the blockchain through Geth's interactive JavaScript console, much like the developer tools in the browser.

I found documentation for Web3, which is a JS library used by the Geth console. Which I used for reference. https://web3js.readthedocs.io/en/v1.3.4/

## The Magic Script

Referencing that documentation and using a little magic, I wrote a piece of JavaScript to be executed in the Geth console.

```js
for (var blk = 3135730; blk <= 3147248; blk++) {
	var transactions = web3.eth.getBlock(blk).transactions;
	
	for (var i = 0; i < transactions.length; i++) {
		var txHash = transactions[i];
		var inputData = web3.eth.getTransaction(txHash).input.toString();
		
		if (inputData.indexOf("89504e470d") != -1) {
			console.log(txHash + " in block " + blk);
		}
	}
}
```

The process flow is as follows:
1. Search every block in between 3135730 and 3147248 (inclusive)
2. Fetch the transactions contained within that block
3. For each transaction, single out its input data
4. If the input data contains the file signature print the transaction hash and block #.

Running that script gave me a single transaction with a large amount of hexadecimal data in the `input` field and the sender's hash.

```json
{
	...
	from: "0xb7e53a55da3eea0fbd09566afda143667d77527d",
	input: "...",
	...
}
```

But after decoding, the data doesn't contain a valid image. Rather, part of one.

## But where's the other half?

Looking up the sender on `goerli.etherscan.io` led to the discovery of one incoming and two total outgoing transactions, one of which was the one we discovered containing the first part.

| Block | From | Direction |
| - | - | - |
| 3142349 | 0xb7e53a55da3eea0fbd09566afda143667d77527d | OUT |
| 3142346 | 0xb7e53a55da3eea0fbd09566afda143667d77527d | OUT |
| 3142342 | 0x8ced5ad0d8da4ec211c17355ed3dbfec4cf0e5b9 | IN |

So extracting from the input data of the other transaction using the hash yield a whole lot more data that I can more than just assume as the second part.

Appending the new hexadecimal data to a file at the end of the other part and decoding it in full created a valid PNG image file.

```sh
$ echo "089504e470d...a08fbd1a76108bb" > image.hex
$ echo "0e6020dd110...9454e44ae426082" >> image.hex
$ cat image.hex | xxd -r -p > image.png
$ file image.png
image.png: PNG image data, 26 x 531, 8-bit/color RGBA, non-interlaced
```

FIN.

## About This Challenge

Block Hunt3r is an **Open Source Intelligence (OSINT)** challenge created by user InfoSecJack on HackTheBox, a large computer network hacking community for growing cybersecurity knowledge through capture-the-flag challenges. [Hack your way in](https://hackthebox.eu).