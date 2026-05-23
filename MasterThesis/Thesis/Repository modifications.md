Actual repository taken into account:
- Longfellow Ligero (american implementation because is ready to run on android) from [here](https://github.com/google/longfellow-zk);
- zk-dilithium STARK prover over ML-DSA modified post quantum signatures [here](https://github.com/guruvamsi-policharla/zkdilithium/tree/master);
- winterfell-sha256 prover from [here](https://github.com/iStrannik/winterfell-sha256);

# Customizations
Added to the main repository Poseidon cross-tester script in python.
## Longfellow
- Compiled and runned on android to benchmark it;
- Branched a repository with Poseidon implementation(Cicruits parameter and proof generator);

## ZkDilithium
- Compiled and runned on android to benchmark it;
- Removed signed message from public informations and moved into witness;

## Winterfell-SHA256
- Compiled and runned on android to benchmark it;