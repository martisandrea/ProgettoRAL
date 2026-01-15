# Progetto RAL - Knister
Implementazione di un agente di Reinforcement Learning che tramite una DQN impara a giocare a Knister, con l'obbiettivo di massimizzare il punteggio ottenuto al termine delle partite.

## Struttura della rete DQN
### Normalizzazione dello stato
Lo stato in una partita di Knister è dato dalla griglia e dal valore ottenuto dai dadi che si vuole inserire in essa: per questo lo stato è dato da un array di 26 elementi, 25 dei quali sono dati dal flatten della griglia e l'ultimo elemento è il valore ottenuto dai dadi. 

Inoltre è stata applicata una normalizzazione dividendo lo stato per 12 (valore massimo ottenibile con due dadi).

### Definizione della QNet
La Q-Network usata è composta dai seguenti layer:
- Input Layer con 26 neuroni, pari alla dimensione dell'array che rappresenta lo stato;
- Tre Hidden Layer con 128 neuroni ciascuno;
- Output Layer con 25 neuroni, pari al numero di azioni possibili (l'azione è data dalla posizione nella griglia dove inserire il valore dei dadi).

I layer sono collegati tra loro da dei layer densi, intervallati dalla funzione di attivazione ReLU.

### Scelta delle azioni e funzione learn
Le funzioni funzioni `getAction(self, state, available_actions, epsilon)` e `learn(self, experiences)` sono state modificate in modo che l'agente possa scegliere sempre un'azione valida, e che l'aggiornamento i Q-Values prenda in considerazione solo le posizioni disponibili della griglia. Questo è stato fatto applicando una maschera che imposti il Q-Value di una cella ad un numero molto basso (-1e5), indicando all'agente quali posizioni non può selezionare.

---
## Train della rete
### Reward Shaping
Data la natura del gioco, in alcune partite la prima combinazione che dà un reward diverso da 0 può essere ottenuta dopo aver compiuto un numero considerevole di azioni; per questo motivo è stato fatto del Reward Shaping in modo da dare all'agente dei feedback intermedi che lo guidino verso delle strategie di gioco.

Il Reward Shaping effettuato enfatizza la costruzione di combinazioni con i valori più alti, quali le scale e il full house.

### Parametri
I parametri usati per l'addestramento della DQN sono i seguenti:

| **Parametro**    | **Valore** | **Significato** |
|------------------|------------|-----------------|
| Batch Size       | 128        | Numero di esperienze salvate in memoria da usare nella fase di apprendimento |
| Learning Rate    | 5e-4       | Valore che determina l'aggiornamento dei pesi ottenuto dall'ottimizzatore |
| Episodi          | 500000     | Numero di episodi per l'addestramento |
| Target Score     | 70         | Valore che se raggiunto determina la fine dell'addestramento |
| Gamma            | 0.99       | Parametro di sconto per le reward future (in questo caso massimo in quanto si vuole ottimizzare la reward alla fine della partita) |
| Memory Size      | 1e6        | Dimensione della memoria che tiene traccia delle interazione passate |
| Learn Step       | 5          | Frequenza con la quale aggiornare la Target Network (in questo caso una volta ogni 5 mosse, quindi in una partita la rete viene aggiornata 5 volte) |
| Tau              | 1e-3       | Parametro che indica il soft update della Target Network |
| Epsilon Start    | 1.0        | Valore iniziale di esplorazione (strategia epsilon-greedy) |
| Epsilon End      | 1e-3       | Valore minimo di esporazione |
| Epsilon Decay    | 1e-3       | Parametro che indica il tasso di decadimento dell'esplorazione (`epsilon = max(epsilon * eps_decay, eps_end)`) |

---
## Risultati
Su 500 partire il punteggio medio ottenuto dalla rete addestrata come indicato sopra è stato di circa **49 punti** (i punteggi ottenuti avviando più volte i test hanno un range che va tra i 48.3 e i 49.8 punti)

Per il test della rete eseguire interamente il notebook, in quanto la sezione che si occupa dell'addestramento è stata commentata.