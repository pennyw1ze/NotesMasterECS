# Report

## Authors
Leonardo Rufini, 2008161
Riccardo Zucchelli, 1984963

## Environment
We choose the [BlackJack environment](https://gymnasium.farama.org/environments/toy_text/blackjack/) in Gymnasium.

BlackJack is a famouse card game. The player and the dealer starts with 2 cards. The player cards are face up, while only one of the 2 dealer's cards is face up. The player must ask for a new card in order to reach the closest possible value to 21. If the dealer reached a grater value than the player, he wins the game. If the player reach an higher value than the dealer which is smaller than 22, he wins the round.

Some general informations about the environment:
- **Action space**: we have 2 possible actions, stick or hit, which corresponds to ask for a card or stay with the current cards;
- **Observation space**: a triple of 3 integers representing the owned value, the dealer value and an integer value which corresponds to 1 if we own an Ace and 0 otherwise;
- **Rewards**: the rewards are +1 if the game is won, -1 if the game is lost and 0 if tied;


## Solutions
We adopted 2 different solutions:
- Training based on **Tabular Q-learning**;
- Training based on **Deep Q-learning**;

### **Tabluar Q-learning**
The first solution adopted is based on Tabular Q-learning. The architecture of tabular Q-learning works as follows:
- The agent initiall choose a random action from the current state with probability epsilon, which is initially set to $1$, and the best currenctly experimented action with the remaining probability;
- During the training, the table containing all the Q values corresponding to the possible actions from a given state are updated with the rewards obtained from the episodes and the value of epsilon is decreased according to epsilon_decay. So while we go on with our experiments, the number of random moves decrease and the agent starts picking the best move for each step;
- The episode's revenue in the table of Q-values are updated proportionally to the learning rate;

#### Implementation
We repeat the training num_episodes times.
We update the table values following this protocol:$$q\_values[state][action] = (1 - lr) * old\_q\_value + lr * target;$$
where $lr$ represents the learning rate and $target$ is computed as:$$target = reward + lr * max\_future\_q * (1 - done);$$
where done correspond to the truncated execution or terminated.

We used numpy and matplot lib, which are standard python libraries, to get the plot of the results obtained from the agent, and to depict the increase of the total reward during the epsiodes while training the agent.

### **Deep Q-learning**
The second solution is based on deep q-learning. The deep Q-learning methodolgy is based on **neural networks**, and in practice is used when the domain of the environment is continuous or large, so is not possible/not efficien to build a Q-table.
Our deep Q-learning model implements an input layer, an output layer and 1 hidden layer.
We use a **replay buffer** in our deep Q-learning model in order to prevent overfitting, and to let the agent re-examine previous episodes in order to avoid further mistakes.
The hyperparameters that characterise our BlackjackDQN are:
- **learning rate**: same usage as before;
- **epsilon and epsilon_rate**: same usage as before;
- **batch_size**: represents the number of experiences that are taken from the replay buffer and used to train the agent, and keeps constant during the execution;


#### Implementation
We used the python [pytorch](https://pytorch.org/) library to deal with neural networks. 
We used the tqdm library to render a progress bar over the trainings in order to keep track of the progress of our agent.
Then we depicted the performance over time graphics using the python matplotlib library.

## Experimental results
 We did some experimental results, changing the hyparameters in both tabular and deep q-learning models.

### Tabular Q-learning
Experimentations over tabular q-learning model.

#### Learning rate = 0,01
![[Pasted image 20250720193136.png]]
![[Pasted image 20250720193143.png]]

#### Learning rate = 0,00001
![[Pasted image 20250720193233.png]]
![/home/pennyw1ze/Projects/ML/Gymnasium_Blackjack/out/tabular/tabular_0.00001.png](file:///home/pennyw1ze/Projects/ML/Gymnasium_Blackjack/out/tabular/tabular_0.00001.png)


### Deep Q-learning
Experimentations over deep q-learning model.

#### Learning rate = 0,01
![[Pasted image 20250720192701.png]]
![/home/pennyw1ze/Projects/ML/Gymnasium_Blackjack/out/dql/dql-table_0.01.png](file:///home/pennyw1ze/Projects/ML/Gymnasium_Blackjack/out/dql/dql-table_0.01.png)

#### Learning rate = 0,00001
![[Pasted image 20250720193023.png]]
![[Pasted image 20250720193031.png]]

The comparison of the performance has been done over the standard blackjack probability table:

![[Pasted image 20250720193350.png]]