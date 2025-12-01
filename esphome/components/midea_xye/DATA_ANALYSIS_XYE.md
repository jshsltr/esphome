Doing some data analysis:
C0 and C3 response structure is exactly the same.
C4 and C6 response structure is exactly the same.

C0/C3: (Field Number, **Not** Byte Number)
| 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11| 12| 13| 14| 15| 16| 17| 18| 19| 20| 21| 22| 23| 24| 25| 26| 27| 28| 29| 30|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|AA |C0 |00 |00 |00 |00 |30 |14 |00 |00 |15 |50 |36 |00 |0C |FF |00 |00 |00 |00 |00 |00 |00 |00 |00 |00 |00 |14 |E0 |01 |
|   |C3 |   |   |   |   |   |   |80 |01 |16 |52 |54 |   |TO |   |   |   |   |   |   |   |   |   |02 |   |   |   |   |   |
|   |   |   |   |   |   |   |   |81 |80 |17 |54 |5E |   |50 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |84 |81 |   |56 |5E |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |82 |   |58 |74 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |84 |   |5A |80 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |   |   |   |92 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |

My Interpretation of the data (Changes in italics):
|Field Number | Description | Notes|
|--- | --- | ---|
|1 | Preamble | 0xAA|
|2 | response code | 0xc0 - Query, 0xc3 - Set, *0xC4 - Extended Query?*, *OxC6 - Follow Me*, 0xcc - Lock, 0xcd - Unlock|
|3 | to master | *0x00*, 0x80|
|4 | destination | 0 .. 0x3f - master device id|
|5 | Source / Own Id | 0 .. 0x3f - device id|
|6 | destination | 0 .. 0x3f - master device id|
|7 | ???? | 0x30 - maybe capabilities|
|8 | capabilities | *0x14 - Aux Heat?, Air Handler?*, 0x80 - extended temp (16 .. 32 °C), 0x10 has SWING|
|9 | Oper Mode | 0x00/*0x04* - off, 0x80 - auto, 0x88 - Cool, 0x82 - Dry, 0x84 - Heat, 0x81 - Fan|
|10 | Fan | 0x8? = Auto Fan flag. 0x0 = Stopped, 0x1 = High, 0x2 = Medium, 0x4 = Low. OR the values (e.g 0x84 = Auto Fan, Current SPeed Low)|
|11 | Set Temp | in °C|
|12 | T1 Temp | in 0.5 °C - *0x28*|
|13 | T2A Temp | in 0.5 °C - *0x28*|
|14 | T2B Temp | in 0.5 °C - *0x28*|
|15 | T3 Temp | in 0.5 °C - *0x28*|
|16 | Current | 0 .. 99 Amps|
|17 | ???? | *0x00 - ???*, 0xff - could be frequency|
|18 | Timer Start | Sum of: 0x01 - 15min, 0x02 - 30min, 0x04 - 1h, 0x08 - 2h, 0x10 - 4h, 0x20 - 8h, 0x40 - 16h 0x80 - invalid|
|19 | Timer Stop | Sum of: 0x01 - 15min, 0x02 - 30min, 0x04 - 1h, 0x08 - 2h, 0x10 - 4h, 0x20 - 8h, 0x40 - 16h 0x80 - invalid|
|20 | ???? | 0x01 - run?|
|21 | Mode Flags | 0x02 - Aux Heat (Turbo), 0x00 - norm, 0x01 - ECO Mode (sleep), 0x04 - SWING, 0x88 VENT|
|22 | Oper Flags | 0x04 - water pump running, 0x80 - locked|
|23 | error | E + bit pos, (0..7)|
|24 | error | E + bit pos, (7..f)|
|25 | protect | P + bit pos, (0..7)|
|26 | protect | P + bit pos, (7..f)|
|27 | CCM Comm Error | 00 .. 02|
|28 | ???? | (0x00), *0x14 - ??*|
|29 | ???? | *Startup Status Flag 1 - Counts up and then 0xE0 is Normal Operation*|
|30 | *?* | *Startup Status Flag 2 - 0x01 is Normal Operation*|
|31 | CRC | 255 - sum(data) % 256 + 1|
|32 | prologue | 0x55|


For C4/C6:

| 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11| 12| 13| 14| 15| 16| 17| 18| 19| 20| 21| 22| 23| 24| 25| 26| 27| 28| 29| 30|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|AA |C4 |00 |00 |00 |00 |00 |00 |00 |30 |98 |00 |00 |00 |00 |20 |04 |01 |CD |BC |D6 |Ou |00 |00 |FF |00 |80 |80 |80 |80 |
|   |C6 |   |   |   |   |   |   |   |   |   |   |   |   |01 |   |81 |80 |CF |   |   |ts |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |84 |   |D0 |   |   |id |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |D1 |   |   |e  |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |T  |   |   |   |   |   |   |   |   |



My Interpretation of the data (Field 1-6 Same As Above, all else mine):
|Field Number | Description | Notes|
|--- | --- | ---|
|1 | Preamble | 0xAA|
|2 | response code | 0xc0 - Query, 0xc3 - Set, *0xC4 - Extended Query?*, *OxC6 - Follow Me*, 0xcc - Lock, 0xcd - Unlock|
|3 | to master | *0x00*, 0x80|
|4 | destination | 0 .. 0x3f - master device id|
|5 | Source / Own Id | 0 .. 0x3f - device id|
|6 | destination | 0 .. 0x3f - master device id|
|7 | ? | 0x00|
|8 | ? | 0x00|
|9 | ? | 0x00|
|10 | ? | Same as 0xC0 Field 7|
|11 | ? | Similar to 0xC0 Field 8?|
|12 | ? | 0x00|
|13 | ? | 0x00|
|14 | ? | 0x00|
|15 | ? | 0x00, 0x01|
|16 | ? | *0x20, 0x60 - Emergency Heat (Aux Only)*|
|17 | Mode | 0x04 - Off , 0x81 - Fan, 0x84 Heat, 0x90 auto, 0x88 Cool,  0x82 Dry|
|18 | ? | 0x01, 0x80|
|19 | Temperature Setpoint | (C = 0.5C scaling, F =1 F scaling, Val - 0x87 = Temp in F)|
|20 | ? | 0xBC|
|21 | ? | 0xD6|
|22 | Outside Temp | in 0.5 °C - 0x28|
|23 | ? | 0x00|
|24 | ? | 0x00|
|25 | ? | 0xFF - Normal, 0x07 - Error?|
|26 | ? | 0x00|
|27 | ? | 0x80|
|28 | ? | 0x80|
|29 | ? | 0x80|
|30 | ? | 0x80|
|31 | CRC | 255 - sum(data) % 256 + 1|
|32 | prologue | 0x55|



C6 _Command_ set:
My Interpretation of the data (Field 1-6 Same As Above, all else mine):
|Field Number | Description | Notes|
|--- | --- | ---|
|1 | Preamble | 0xAA|
|2 | Command | *0xC6 - Extended Set? - Follow-Me, Static Pressure, Emergency Heat Modes*|
|3 | to master | *0x00*, 0x80|
|4 | destination | 0 .. 0x3f - master device id|
|5 | Source / Own Id | 0 .. 0x3f - device id|
|6 | destination | 0 .. 0x3f - master device id|
|7 | ? | 0x00|
|8 | ? | 0x00|
|9 | Settings | 0x10 - Set Static Pressure, 0x95-9F Static Pressure Value (5-15 for Static Pressure), 0x80 - Emergency Heat|
|10 | ? | 0x00|
|11 | Follow-Me Command | 0x6 Start, 0x2 Update, 0x4 Stop|
|12 | Follow-Me Temp | in C|
|13 | ? | 0x00|
|14 | Command Check | 0xFF - Field 2|
|15 | CRC | 255 - sum(data) % 256 + 1|
|16 | prologue | 0x55|
