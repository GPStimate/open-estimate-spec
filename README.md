# Open Estimate Spec

An open specification for exchanging construction estimates between tools.

The specification defines a data transfer object for construction work, quantities, costs, and selling prices without depending on a particular application, database, or estimating workflow. It describes supplied data, not calculation logic.

## Status

Version `0.1.0` is an initial draft, not a stable release. Its immediate focus is an estimate recap: material, labor, subcontract, equipment, and other costs, plus stated markups and estimate totals. No material list or labor breakdown is required.

The format also retains optional flat work items and supports lump-sum and unfinished estimates. Costs and selling prices are independently optional.

- [Read the specification](docs/specification.md).
- [Inspect the JSON Schema](schemas/estimate.schema.json), using JSON Schema Draft 2020-12.
- [Browse the examples](examples/README.md).
- [Read the broader conceptual background](docs/concepts.md), which is non-normative and includes concepts deferred from this version.

## Smallest example

```json
{
  "specVersion": "0.1.0",
  "description": "Replace roof flashing, including materials and labor."
}
```

To supply a selling price, add `"currency": "USD"` and `"sellingPrice": "5000.00"`. Monetary values are decimal strings. An omitted cost does not mean zero and does not equal the selling price.

## Recap exchange

The optional `recap` object carries `materialCost`, `laborCost`, `subcontractCost`, `equipmentCost`, `otherCost`, and `markups`. Estimate-wide totals remain in `cost` and `sellingPrice`.

Each markup has a description and can supply an amount, a percentage with a stated basis, or both. These are supplied values, not formulas to execute. See the [recap example](examples/recap.json) for a complete exchange without work items.

## Validate the draft

The [schema](schemas/estimate.schema.json) can be used with any validator supporting JSON Schema Draft 2020-12. This repository does not currently include a validator or test runner.

Validation checks the data contract, not arithmetic. Totals and item amounts are carried as supplied, even when they differ.

## Boundaries

Detail-level material and labor costs and selling prices, per-unit costs and prices, material lists, and labor hours by detail are deferred. Existing work-item amounts remain whole-item values.

This first version also does not define resource catalogs, package conversions, calculation formulas, grouping, taxes, alternatives, attachments, or takeoff geometry. It exchanges stated quantities, amounts, and markup percentages, not a calculation model.

Documentation uses American English. Examples use U.S. customary units; the format also supports the metric units listed in the specification. There are no application-specific prerequisites.

## License

The specification text, JSON Schema, examples, and other repository contents are licensed under the [Apache License, Version 2.0](LICENSE).
