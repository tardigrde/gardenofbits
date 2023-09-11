---
{"dg-publish":true,"dg-path":"Blog/Automate testing & deployment of Solidity smart contracts.md","permalink":"/blog/automate-testing-and-deployment-of-solidity-smart-contracts/","tags":["writing","blogging","blogpost","post"]}
---


# Automate testing & deployment of Solidity smart contracts

Ever wondered what tools are required for working with smart contracts? Are there IDEs and frameworks specialized for contracts? What about testing and deployment? How do developers test their contracts, and how do they deliver it? 

What follows is a practical guide on testing, deploying and interacting with Solidity smart contracts in `brownie`, a Python-based framework designed for smart contract development. With brownie one can run concise deployment scripts, write tests in `PyTest` and interact with live contracts too.

> **_NOTE_**: It is assumed that the reader has some basic terminal skills, as well as some experience in Python and Solidity Smart Contracts. For anyone who is unfamiliar with these technologies, you will find some learning material at the end of the article.

## Problems with Solidity tools

When I wanted to deploy my first Solidity smart contract, I came across the [Remix IDE](https://remix-project.org/). With full Solidity support, the ability to test and deploy contracts to local and public blockchains, and having a built-in debugger, the Remix IDE is rightfully regarded as one of the best Solidity IDEs.

After my first experiments with this new tool, though, I realized that there is a learning curve in getting efficient at it. I found it cumbersome to work with external blockchain providers, and to manually interact with the deployed contract. The Remix console uses JavaScript, which I am not a big fan of.

These impressions made me ask:
1. How can I develop a contract from scratch, in a test-driven manner?
2. What is the leanest way to deploy these contracts and interact with them? 
3. How can I automate operations tasks related to contracts and integrate them in (existing) CI/CD processes?

## Brownie

While searching for answers, I found 3 comparable frameworks, which offer most of the features I need. [Truffle](https://github.com/trufflesuite/truffle) is the most mature and certainly has the most features. [Hardhat](https://github.com/NomicFoundation/hardhat) is a more modern tool, which focuses on debugging. Both of these tools are written in JavaScript. [Brownie](https://github.com/eth-brownie/brownie) is also a decent alternative written in Python. 

I opted for `brownie`, because it has a CLI, an interactive console, uses [pytest](https://docs.pytest.org/) for testing and makes deployments easy with different network and account configuration. Another consideration was, that Python is my scripting language of choice. This allowed me to use well-known tools to solve new problems. 


## Source code & Developer environment

I set up a git [repository](https://codeberg.org/tardigrde/mndwrk-brownie-blogpost) on [codeberg.org](https://codeberg.org/), which helps you *get started* with the framework. It guides you through setting up the environment needed to follow this article and contains most of the source code present in this article.

The IDE I am using is [VSCode](https://code.visualstudio.com/) with the [Solidity plugin](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity) installed. The plugin has many features, like syntax highlighting, linting and even contract compilation. For the latter and everything else, I use `brownie`.

You may skip the practical part and read the article as is. But if you want to follow the practical guide, you may **clone the repo** now and follow its [README.md](https://codeberg.org/tardigrde/mndwrk-brownie-blogpost/src/branch/main/README.md) until your development environment is set up.

```shell
git clone https://codeberg.org/tardigrde/mndwrk-brownie-blogpost.git
```

## Lottery smart contract

The [contract](https://codeberg.org/tardigrde/mndwrk-brownie-blogpost/src/branch/main/contracts/Lottery.sol) being worked with is a dead-simple Lottery application. Users can enter the lottery by paying a certain amount of ether to the contract. The owner of the contract can decide when they pick a winner. The lottery can be played repeatedly. 

Find the [Lottery](https://codeberg.org/tardigrde/mndwrk-brownie-blogpost/src/branch/main/contracts/Lottery.sol) contract in the attached git repository. It is based on *jspruance*'s' [block-explorer-tutorials/Lottery.sol](https://github.com/jspruance/block-explorer-tutorials/blob/main/smart-contracts/solidity/Lottery.sol), with some added functionality and clarifying comments.

## Brownie console

Let us now dive into the fun part by trying out the brownie console. It is a REPL, similar to Python's, that helps you quickly interact with local or non-local chains. It is the fastest way to familiarize yourself with the brownie API, as it is the best place to make quick experiments to test new ideas or 3rd-party contracts.

After activating the Python environment, invoke the console using:

```bash
$ brownie console
```

> **_NOTE_**: You can actually start the console on different kinds of local and public, so-called `live` chains. [Network](https://eth-brownie.readthedocs.io/en/stable/network-management.html) and corresponding [account](https://eth-brownie.readthedocs.io/en/stable/account-management.html) management is outside the scope of this article. If you want to deploy contracts on live chains, refer to the [docs](https://eth-brownie.readthedocs.io/en/stable/).

When the console is started, brownie starts and connects to a [ganache](https://github.com/trufflesuite/ganache) instance, which is basically a local snapshot of the Ethereum blockchain. It also provides 10 pre-created accounts which you can use for testing your contracts. 

```python
>>> from brownie import accounts
>>> len(accounts)
10
```

We will use the first account as the `owner` of the contract.

```python
>>> owner = accounts[0]
>>> owner
<Account '0x66aB6D9362d4F35596279692F0251Db635165871'>
```

We may import the Lottery class, which provides a Pythonic API for the contract.  

```python
>>> from brownie import Lottery
```

Then we can deploy the Lottery contract by calling the class' built-in `deploy` method with constructor parameters. Lottery has no such params, but we must pass at least a config dictionary. The `from` key's value tells brownie which account do we deploy the contract with.

```python
>>> lottery = Lottery.deploy({"from": owner})
Transaction sent: 0x04d5a2db4f8ca20fb0279a0d8f74b3a6e45c8630e4758654304e8995a05160b6
  Gas price: 0.0 gwei   Gas limit: 12000000   Nonce: 0
  Lottery.constructor confirmed   Block: 1   Gas used: 427318 (3.56%)
  Lottery deployed at: 0x3194cBDC3dbcd3E11a07892e7bA5c3394048Cc87
```

Let's stop here to inspect the output above. We see both the transaction hash and the contract address. Feel free to inspect these fields in the console.

```python
lottery.address
lottery.tx
lottery.tx.status
```

We can call public view functions to get some metadata about the lottery.

```python
>>> lottery.getPlayers()
()
>>> lottery.getBalance()
0
>>> lottery.cost() # in WEI
10000000000000000
```

Now let's enter the lottery with the second account in the accounts list.

```python
>>> lottery.enter({"from": accounts[1], "value": ".01 ether"})
# ...hidden output...
```

Note the new `value` key (unlucky naming) passed in the config dictionary. What it means is `0.01 ether` was included in the transaction, which is the participation cost. 

Let's call those view functions again.

```python
>>> lottery.getPlayers()
("0x33A4622B82D4c04a53e170c638B944ce27cffce3")
>>> lottery.getBalance()
10000000000000000
>>> assert lottery.getPlayers()[0] == accounts[1]
>>> 
```

We got the first passing assert statement, which is an invitation to write a unit test. You may also leave the console now.

## Testing
Brownie uses `pytest` under the hood. You can write your usual unit tests like so:

```python
def test_enter():
	lottery = Lottery.deploy({"from": accounts[0]})
	assert len(lottery.getPlayers()) == 0
	lottery.enter({"from": accounts[1], "value": ".01 ether"})
	assert len(lottery.getPlayers()) == 1
	assert lottery.getPlayers()[0] == accounts[1]
```

Put this into a pytest file e.g. `./tests/test_lottery.py` (see [conventions for test discovery](https://docs.pytest.org/en/7.1.x/explanation/goodpractices.html#conventions-for-python-test-discovery)), and call: 

```shell
$ brownie test
```

The tests can be extremely helpful for implementing new features, while avoiding regression issues. They give confidence that the contract we or a 3rd wrote works as expected.

Write tests until you are happy with the coverage and results, then commence with deployment.

## Running scripts
You can write scripts to deploy contracts and interact with them in brownie. Consider the simplest form of the deployment script under `./scripts/lotter.py`:

```python
from brownie import accounts, Lottery

def main():
    Lottery.deploy({"from": accounts[0]})
```

You can execute the function called `main` with `brownie run`, like so:

```shell
$ brownie run scripts/lottery.py
```

The local `ganache` instance is torn down after the script concludes. If you want to interact with the deployed contract, you have to write extra code after deployment, or run the script on live chains. We will go with the former.

## Lottery simulation

In this last section, we will conduct a lottery simulation. The code we wrote during the development and testing phase can be reused here. 

The following script deploys the lottery, enters with all the 10 pre-created test accounts, and finally announces a winner.

```python
def main():
	# deploy the contract
	lottery = Lottery.deploy({"from": accounts[0]})
	
	# enter with test accounts
	for i in range(10):
		lottery.enter(
			{"from": accounts[i], "value": ".01 ether"}
		)

	print(f"Players: {lottery.getPlayers()}")
	print(f"Lottery balance: {lottery.getBalance()}")
	
	# pick winner
	lottery.pickWinner()
	
	print(f"The winner of the first lottery is: {lottery.getWinnerByLotteryId(1)}")
```


Running this with `brownie run scripts/simulate_lottery.py` produces a lot of output on the terminal. I encourage you to analyze it.

## Conclusion

In this article, I attempted to demonstrate how you can use Solidity frameworks, like brownie, to make contract development easier.

Although `brownie` is a relatively new tool too, I can leverage my Python and pytest know-how that helps flatten the learning curve. It also allows me to integrate tests and scripts related to smart contracts in already existing projects.

I hope reading this article makes you motivated to try out brownie or its alternatives. At the very least I hope it gives you a glimpse into how smart contract development works.


## Sources

[Brownie docs](https://eth-brownie.readthedocs.io/en/stable/)

[Solidity docs](https://docs.soliditylang.org/)

[pytest docs](https://docs.pytest.org/)

[Remix IDE docs](https://remix-ide.readthedocs.io/en/latest/index.html)

Framework comparisons:

[Web3: Truffle VS Hardhat VS Embark VS Brownie](https://dev.to/bhosalepratim/web3-truffle-vs-hardhat-vs-embark-vs-brownie-3ikk)

[Write a Simple Smart Contract in Truffle, Hardhat and Brownie](https://mirabdulhaseeb.medium.com/truffle-vs-hardhat-vs-brownie-lets-compare-ea08b927d084)

### Learning material

[Learn Shell - Free Interactive Shell Tutorial](https://www.learnshell.org/)

[Learn Python - Free Interactive Python Tutorial](https://www.learnpython.org/)

[CryptoZombies - Free Solidity Tutorial & Ethereum Blockchain Programming Course](https://cryptozombies.io/)
