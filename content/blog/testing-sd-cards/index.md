+++
title = "Testing fake microSD cards"
date = 2022-08-28
+++

At some point I ran out of functional SD cards that I could use for projects so I decided to order more. Instead of going to a Reputable Seller&trade;, I went on a shopping spree and bought 15 of the most popular ones from aliexpress. For each product listing, I chose the largest capacity available for less than $5.

## Testing

[f3](https://github.com/AltraMayor/f3) (Fight Flash Fraud) is a tool that writes deterministic, pseudorandom data to a drive, up to its capacity. When reading that data back, you can spot corrupted sectors. These are most often caused by card manufacturers falsely reporting a larger storage capacity. You can also benchmark read/write speeds.


| Code | Advertised Capacity  | Class                |
|------|----------------------|----------------------|
| A    | 64G                  |                      |
| B    | 512G                 |                      |
| C    | 32G                  | microSDHC UHS-I      |
| D    | 32G                  | microSDHC UHS-I      |
| E    | 32G                  | microSDHC UHS-I      |
| F    | 32G                  | microSDHC UHS-I U3 A1|
| G    | 64G                  | microSDHC UHS-I A1   |
| H    | 128G                 |                      |
| I    | 64G                  |                      |
| J    | 128G                 |                      |
| K    | 128G                 |                      |
| L    | 32G                  |                      |
| M    | 64G                  |                      |
| N    | 64G                  |                      |
| O    | 32G                  |                      |










galaxus:
0.47
0.39
0.47
0.48
0.94
0.34
0.30
0.24
0.59
0.25
0.48
0.89
