# Golvellius: Valley of Doom 

## RAM Locations

| Address | Size | Description                                             |
| ------- | ---- | ------------------------------------------------------- |
| $081A   |    1 | Player health; valid range: 0-6                         |
| $0836   |    1 | Potions remaining                                       |
| $0839   |    1 | Potions max                                             |
| $0840   |    1 | Gold, thousandths (BCD encoded, e.g. 0x02 = 2000)       |
| $0841   |    1 | Gold, hundredths\*10 (BCD encoded, e.g. 0x39 = 390)     |
| $0842   |    1 | Gold max, thousandths (BCD encoded, e.g. 0x10 = 10000)  |
| $0842   |    1 | Gold max, hundreths\*10 (BCD encoded, e.g. 0x22 = 220)  |

