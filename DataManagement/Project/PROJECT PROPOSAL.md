Leonardo Rufini 2008161,
Riccardo Zucchelli 1984963,
DBMS comparison.

### Brief description of the work
After attending the Advanced Information System Security and Blockchain lecture in Sapienza, we found an interesting application of **GraphQL database** concerning the management and querying of the **blockchain**.
The leading project is named [The Graph](https://thegraph.com/) and is spreading all over the world, replacing old interface in web3.
We would like to take in consideration this system and analyze his performance with some structured queries. We will download the last month transactions (enough to create a considerable database) and we will adapt it in a standard SQL structure and in a GraphQL structure. We will then perform queries related to Blockchain UTXOs tracking and trying to represent the flow of transactions. This will help us to further understand the flow of Bitcoins, and we will be able to _evaluate the performance differences_ between graph database and SQL database.

### Description of the dataset
As dataset, since the project will analyze a blockchain, we will download a huge set of **transactions** (this is always possible in a public blockchain).
A transaction is characterized by several fields. We will take into account the most important fields which are:
- **Sender address**;
- **Receiver address**;
- **Value** transfered;
- **Timestamp** (date of the transaction);

In different blockchains, transactions has different structures. We will take in consideration the **bitcoin blockchain** which is the most famous and the biggest one, since it will give us for sure enough data to produce accurate evaluations.