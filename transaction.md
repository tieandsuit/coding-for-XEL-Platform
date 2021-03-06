# CODING FOR XEL CRYPTO PLATFORM #1: TRANSACTIONS ON THE XEL BLOCKCHAIN


### XEL BLOCKCHAIN TRANSACTIONS
The XEL Blockchain is the heart of XEL. Every Transaction ever made is recorded on the Blockchain.
The Blockchain is a decentralized database, which is saved on your computer.
To prevent spam, for every transaction you have to pay fees in the native currency, which will be XEL for us using the XEL Blockchain.
Throughout the article I want to focus on tools you can use while creating Transactions on XEL.

### SEND XEL
Codewise, creating a transaction on XEL is very similar to reading data from the Blockchain;
There are some topics we have to cover before creating our first tranasction.
To create a transaction, we first need a XEL Account and some XEL, in order to pay for Blockchain fees. So, let us login to XEL and create our first account.

### CREATE A XEL ACCOUNT
In order to have a secure XEL account, you need a strong passphrase. Working With virtual currencies we use the term passphrase instead of password, this describes strong entropy and should be more secure than a regular password. When you are using the official XEL Client, you can use the implemented passphrase creation function, which will give you a passphrase which contains 12 words.

### CREATE YOUR XEL PASSPHRASE
You can also create your own passphrase, make sure it contains enough characters and entropy. To generate a passphrase it is recommended to use Software such as KeePass, LastPass, Dashlane or Online Tools as PasswordsGenerator.net or Fourmilab.ch.

### XEL PASSPHRASE GENERATOR
When running XEL on http://localhost:17876 you will get to the client, a click on the link quoting

```
DON`T HAVE AN ACCOUNT? CLICK HERE TO CREATE ONE!
```

will bring you to the passphrase generator. Afterwards (or with your self created passphrase), you can login to your Account. If you do not have XEL yet, the quickest method is when you already got some virtual Currencie like Bitcoin. You can exchange them via Bittrex or buy your coins on another exchange and transfer them to your wallet.

In the next steps, we use your passphrase to sign and broadcast transactions. Your passphrase can also be seen as your private Key.

### CREATE THE TRANSACTION

With a secure passphrase, we can now look into the code of creating a send-XEL-Transaction. We send out 1 XEL to a recipient, which you see in the code and change it there for another recipient. Insert your passphrase in the following code and run it to create the transaction.

```
<!DOCTYPE html>
<html>
<head>
<!– Latest compiled and minified CSS –>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">

</head>
<body>
<div class="col-md-12">
<h2>Outgoing Transaction JSON</h2>

<div class="well" style="word-wrap:break-word;" id="transactionJSON"></div>
</div>

<script src="https://code.jquery.com/jquery-2.2.0.min.js"></script>
<!– Latest compiled and minified JavaScript –>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>
<script>

$.post(`http://localhost:17876/nxt`, {"requestType": "sendMoney", "recipient": "XEL-MAYC-ZZ3Y-YX56-6NH52", "amountNQT": "100000000", "secretPhrase": "YourPassphrase", "feeNQT": "0", "deadline": "800", "broadcast": false}, function(request) {

var response = JSON.parse(request);
$("#transactionJSON").html(JSON.stringify( response , null, 4));

});

</script>
</body>

</html>
```

This script is creating a sendMoney transaction. As you see we have been changing the function used in previous articles $.getJSON to $.post. When creating transactions, it is necessary to switch to POST format instead of GET requests as with the previous functions. For reference to the API details, have a look at http://localhost:17876/test?requestType=sendMoney.

The output you see on the page contains various information, the variable feeNQT has been filled in with the correct information, you can check how much the fee is for the transaction you have created. You see the whole transactionJSON you have inserted in JSON format and you receive the transaction transactionBytes. The transactionBytes embeddes all the transaction information you have in the tranasctionJSON.

The above created transaction will not be broadcasted to the network yet, because we set "broadcast": false in our object. When setting this true the transaction will be broadcasted and will cost you 0.1 XEL. As you can see, we do not need many variables to create our first transaction.

The recipe for a transaction is:

* requestType sendMoney
* recipient You can use either the RS format e.g. XEL-XXX-XXX-XXX-XXXX or the numerical account format
* amountNQT The amount you want to send needs to be calculated in NQT, the smallest number in XEL (1 XEL / 10⁸ = 1 NQT)
* secretPhrase Your secret passphrase
* feeNQT Place 0 if you want the server to calculate minimum fees for you
* deadline measured in minutes, when the transaction expires
* broadcast false will not broadcast and just create the transaction. Set to true will broadcast the transaction to other peers and will cost you XEL

### BROADCAST TRANSACTION

Now as we know how to create a sendMoney transaction that has not yet been broadcasted, we can use a script that looks similar to the above one to broadcast the transaction. To make it more understandable I am extending the above script with a button that takes the previously created transaction and transfers it to the network on submitting the button. Effectively when running the script you have your first (small, not yet secure) interactive XEL wallet.

```
<!DOCTYPE html>
<html>
<head>
<!– Latest compiled and minified CSS –>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7″ crossorigin="anonymous">

</head>
<body>
<div class="col-md-12″>

<h2>Outgoing Transaction JSON</h2>

<div class="well" style="word-wrap:break-word;" id="transactionJSON"></div>

<button class="btn btn-primary" id="submitTransaction">Submit this transaction</button>

<hr>

<div id="broadcastedTransaction"></div>

</div>

<script src="https://code.jquery.com/jquery-2.2.0.min.js"></script>
<!– Latest compiled and minified JavaScript –>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>
<script>

$.post(`http://localhost:17876/nxt`, {"requestType": "sendMoney", "recipient": "XEL-MAYC-ZZ3Y-YX56-6NH52", "amountNQT": "100000000", "secretPhrase": "YourPassphrase", "feeNQT": "0", "deadline": "800", "broadcast": false}, function(request) {

var response = JSON.parse(request);
$("#transactionJSON").html(JSON.stringify( response , null, 4));

$("#submitTransaction").on("click", function() {

$.post(`http://localhost:17876/nxt`, {"requestType": "broadcastTransaction", "transactionBytes": response.transactionBytes}, function(broadcast) {

var broadcastResponse = JSON.parse(broadcast);
$("#broadcastedTransaction").html(JSON.stringify( broadcastResponse , null, 4));

});
});
});

</script>
</body>

</html>
```

Loading the site will directly load the transaction not yet broadcasted. As the button click function is nested within the first call, it can access the data from the transaction not broadcasted. To broadcast the transaction we use the following recipe

* requestType broadcastTransaction
* transactionBytes The transactionBytes we have created with the previous declared function.

With these few lines of code we can create a transaction and broadcast it. Yet, we are still defining all the variables directly within the code. In the next articles I am going to show you how to add encrypted messages to a transaction and how to get these data from a user input you have on the page. We will have a deeper look into phasing and pruning of transactions.
