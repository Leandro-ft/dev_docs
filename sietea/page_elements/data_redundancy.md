sospechoso de convertirse en mocks:

```rb
# in AssetClpNoClp....
{
  name: "Acciones",
  start_amount: 100000000.0,
  end_amount: 120000000.0,
  modified_end_amount: 120000000.0,
  utility: 20000000.0,
  profit: 0.20,
  profit_contribution: 0.111,
  mkt_weight: 0.60,
  modified_mkt_weight: 0.60,
  asset_list: []
},

#in GainLoss
asset_name: 'Bonos Corporativos',
end_quantity: 500,
end_amount: 2500000,
realized_gain_sales_short_term: 10000,
realized_gain_sales_long_term: 20000,
realized_gain_sales_total_gain: 30000,
unrealized_gain_sales_short_term: 5000,
unrealized_gain_sales_long_term: 15000,
unrealized_gain_sales_total_gain: 20000,
realized_gain_dividends_short_term: 2000,
realized_gain_dividends_long_term: 6000,
realized_gain_dividends_total: 8000,
total_utility: 58000,
profit_anual: 0.0232,
total_utility_historic: 500000,
historic_profit_anual: 0.05
```

```
columnas en IndexTable
```