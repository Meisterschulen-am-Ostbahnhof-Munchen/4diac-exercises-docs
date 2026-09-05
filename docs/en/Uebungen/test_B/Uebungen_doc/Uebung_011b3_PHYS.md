# Uebung_011b3_PHYS: Numeric Value Input SUB (PHYS)

This article describes the logiBUS® exercise `Uebung_011b3_PHYS`. Two physical numeric values entered on the VT terminal are subtracted from each other, and the result is displayed again.

----

## Goal of the Exercise

Accept two numeric fields (`InputNumber_I3_N`, `InputNumber_I4_N`) on the VT terminal, compute their difference, and display the result in a third numeric field.

-----

## Description and Components

![Uebung_011b3_PHYS_network](./Uebung_011b3_PHYS_network.svg)

- **`InputNumber_I3_N`**, **`InputNumber_I4_N`**: `isobus::UT::io::NumericValue::NumericValue_PHYS`, physical numeric fields on the VT.
- **`F_SUB`**: `iec61131::arithmetic::F_SUB`, subtracts the two entered values.
- **`E_MERGE`**: `iec61499::events::E_MERGE_2`, triggers the calculation as soon as either field is changed.
- **`Q_NumericValue_PHYS`**: displays the subtraction result (`OutputNumber_N3_N`).

-----

## Behavior

1. The operator changes one of the two numeric fields `InputNumber_I3_N`/`InputNumber_I4_N`.
2. `E_MERGE` then triggers `F_SUB`, which computes `IN1 - IN2`.
3. The result is displayed in `Q_NumericValue_PHYS`.
