# Loss quantization

## Binary Cross Entropy
Assume Label is **1**.
|Prediction |  Loss       | Commetns  |
|-----------|-------------|------------|
|0          |   100       | |
|0.01       |   **4.61**  | :disappointed: exploding gradient? |
|0.1        |   2.30      | |
|0.2        |   1.61      | |
|0.3        |   1.20      | |
|0.4        |   0.92      | |
|0.5        |   **0.69**  | :neutral_face: meaningful |
|0.6        |   0.51      | :flushed: it works |
|0.7        |   **0.36**  | :grinning: good |
|0.8        |   0.22      | :wink: great|
|0.9        |   **0.11**  |:relaxed: pretty good |
|0.95       |   0.05      | |
|0.99       |   **0.01**  |:triumph: (perfect) |
 
