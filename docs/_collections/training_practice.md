# Loss quantization

## Binary Cross Entropy
Assume Label is 1.
|Prediction         |  Loss   |
|---------|---------|
|0        |   100   |
|0.01 (imbalanced?) |   **4.61**  :disappointed:|
|0.1      |   2.30  |
|0.2      |   1.61  |
|0.3      |   1.20  |
|0.4      |   0.92  |
|0.5 (meaningful)   |   **0.69**  :neutral_face:|
|0.6      |   0.51  |
|0.7 (it works)     |   **0.36**  :grinning:|
|0.8      |   0.22  |
|0.9 (pretty good)  |   **0.11**  :relaxed:|
|0.95     |   0.05  |
|0.99 (perfect)     |   **0.01**  :triumph:|
 
