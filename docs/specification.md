# Open Estimate Spec 0.1.0

## Status and purpose

This is the first draft of Open Estimate Spec, an application-independent data transfer object format for construction estimates. It defines the structure and meaning of supplied data, not an estimating method, calculation engine, or application's internal data model.

The words MUST, MUST NOT, and MAY identify requirements, prohibitions, and permitted options in this document. All other explanatory text and examples support those rules.

This document and the [JSON Schema](../schemas/estimate.schema.json) define version `0.1.0`. A conforming document satisfies both the schema and the semantic rules below. If they disagree, this document takes precedence and the schema needs correction.

## Scope

This version represents:

- An estimate-wide recap of material, labor, subcontract, equipment, and other costs, plus stated markups.
- An estimate-wide scope of work, optionally broken into a flat list of work items.
- Cost-only, selling-price-only, and combined estimates.
- Lump-sum estimates without measured quantities or work items.
- Unfinished estimates with descriptions but no monetary amounts.
- Work quantities, project context, assumptions, exclusions, and notes.

The immediate exchange target is the recap, without requiring a material list, labor breakdown, or individual work items. Existing work items remain optional.

Later versions may represent material and labor costs and selling prices by work item, cost and selling price per unit, material lists, and labor hours by work item. Those fields are not defined here.

Resource catalogs, material coverage, packaging, labor production rates, tax and markup formulas, grouping, alternatives, revisions, classifications, attachments, and drawing geometry are also outside this version. Stated markup amounts and percentages are data, not calculation instructions.

This version exchanges stated quantities and amounts. It MUST NOT require calculations, derived values, or agreement between estimate totals and item amounts as a condition of document validity. It does not prescribe editing, import workflows, or recalculation behavior.

## Document format

A document MUST be a UTF-8 JSON object. Object member names are case-sensitive and MUST NOT be duplicated within an object. JSON comments, non-finite numbers, and trailing commas are not permitted.

The document MUST include `specVersion` with the exact string `"0.1.0"` and a nonblank `description` of the work. Other fields are optional, subject to the currency rule below.

An omitted field means information was not supplied. It MUST NOT be interpreted as zero, an empty description, or an amount equal to another field. Explicit `null` values are not permitted. A supplied zero is a known amount or quantity, not missing information.

Objects MUST NOT contain fields other than those defined for them in this version. There is no extension mechanism in `0.1.0`.

### Estimate fields

| Field | Type | Meaning |
| --- | --- | --- |
| `specVersion` | String, required | Exactly `"0.1.0"`. |
| `description` | Nonblank string, required | Scope of work covered by the estimate. |
| `title` | Nonblank string | Short name for the estimate. |
| `project` | Project object | Context identifying the construction project. |
| `currency` | String | Currency applying to every monetary amount in the document. Required if any amount is supplied. |
| `cost` | Signed decimal string | Total estimated cost for the estimate-wide scope. |
| `sellingPrice` | Signed decimal string | Total intended charge for the estimate-wide scope. |
| `recap` | Recap object | Estimate-wide category costs and stated markups. No work-item or resource breakdown is required. |
| `workItems` | Nonempty array of work item objects | Ordered breakdown of distinct portions of the estimate-wide scope. |
| `assumptions` | Array of nonblank strings | Conditions on which the estimate is based. |
| `exclusions` | Array of nonblank strings | Work or expenses explicitly not included. |
| `notes` | Nonblank string | Additional human-readable context. |

A nonblank string MUST contain at least one non-whitespace character. An empty `assumptions` or `exclusions` array is permitted and supplies no entries. It does not assert that no other assumptions or exclusions exist.

### Project object

| Field | Type | Meaning |
| --- | --- | --- |
| `name` | Nonblank string | Name of the project. |
| `location` | Nonblank string | Human-readable project address or location. |

A project object MUST contain at least one of these fields. Project context is optional. An estimate does not require a customer record, street address, or external project identifier.

### Recap object

The optional recap carries estimate-wide cost categories and markups. It MUST contain at least one field from the table below. Each category value is an already-extended cost, not a selling price, unit rate, or resource list.

| Field | Type | Meaning |
| --- | --- | --- |
| `materialCost` | Signed decimal string | Total estimated cost of materials. |
| `laborCost` | Signed decimal string | Total estimated cost of labor. |
| `subcontractCost` | Signed decimal string | Total estimated cost of subcontracted work. |
| `equipmentCost` | Signed decimal string | Total estimated cost of equipment used for the work. |
| `otherCost` | Signed decimal string | Total estimated cost not represented by the preceding categories. |
| `markups` | Nonempty array of markup objects | Labeled estimate-wide markup information. |

Any subset of categories MAY be supplied. An omitted category means its cost was not supplied, not that it is zero. A recap does not assert that the categories are a complete accounting of the estimate cost. Their agreement with the estimate total or work-item amounts is not a condition of validity.

### Markup object

A markup entry describes an addition for overhead, profit, or another stated purpose. It carries a monetary amount, a percentage with an identified basis, or both.

| Field | Type | Meaning |
| --- | --- | --- |
| `description` | Nonblank string, required | Purpose or label of the markup. |
| `amount` | Signed decimal string | Stated monetary addition, in the estimate currency. A negative amount can describe a credit or reversal. |
| `percentage` | Nonnegative decimal string | Stated percentage, with `"10"` meaning 10%, not a factor of 0.10. |
| `basis` | Nonblank string | Human-readable description of what the percentage applies to. Required if `percentage` is supplied, and permitted only with `percentage`. |

A markup MUST supply `amount`, `percentage`, or both. An amount-only markup does not require a percentage or basis. A percentage-only markup does not imply a monetary amount. Percentages have no maximum of 100 because a markup can exceed its basis.

For example, `{"description": "Overhead and profit", "percentage": "25", "basis": "Estimate cost before markups", "amount": "2500.00"}` supplies both a stated rate and amount. The basis is descriptive text, not an executable expression. The specification does not calculate either value or require their agreement.

Markup and margin are not interchangeable. A percentage of selling price describing margin MUST NOT be relabeled as a percentage markup on cost. The stated basis must make the meaning clear.

Markup array order is presentation order only. It does not define compounding, application order, or how markups are included in the supplied cost or selling price. Those totals remain independently supplied values, not instructions to add the markup amounts again.

### Work item object

A work item is a distinct portion of the work described by the estimate. Work items are additive portions of that scope, not alternate options, repeated subtotals, or resources used to perform another item. Array order is presentation order and does not establish a construction schedule.

| Field | Type | Meaning |
| --- | --- | --- |
| `description` | Nonblank string, required | Scope of work covered by this item. |
| `quantity` | Quantity object | Measured amount of work. Omit for unmeasured or lump-sum work. |
| `cost` | Signed decimal string | Extended estimated cost of this item's work. |
| `sellingPrice` | Signed decimal string | Extended intended charge for this item's work. |
| `notes` | Nonblank string | Additional context for this item. |

A work item's amounts cover the entire item. They MUST NOT be interpreted as per-unit rates or multiplied by `quantity` to obtain totals. A measured item MAY have stated amounts without supplying unit prices or resource calculations.

Omitting `workItems` permits an estimate-wide lump sum or unfinished scope description. A supplied array MUST contain at least one item. Work items MAY omit quantities, amounts, or both.

### Quantity object

| Field | Type | Meaning |
| --- | --- | --- |
| `value` | Nonnegative decimal string, required | Amount of work. |
| `unit` | Unit code, required | Unit from the table below. |

A quantity in this version is a work quantity, not a material purchase quantity, package count, or pricing quantity. Its purpose is established by the work item's description.

## Decimal values

Monetary amounts, quantity values, and markup percentages MUST use JSON strings containing base-10 decimals. Strings avoid loss of decimal precision across programming languages.

Signed monetary values use this grammar:

```text
^-?(0|[1-9][0-9]*)(\.[0-9]+)?$
```

Quantity values and markup percentages use the same grammar without the optional minus sign:

```text
^(0|[1-9][0-9]*)(\.[0-9]+)?$
```

Examples of permitted monetary values are `"0"`, `"4000.00"`, `"0.125"`, and `"-250.00"`. Numeric JSON values such as `4000`, and strings such as `"4,000"`, `"$4000"`, `"+4000"`, `"04"`, and `"4e3"`, are not permitted.

Negative monetary amounts MAY represent credits or deductions. Their descriptions or notes should explain their scope. Quantities and markup percentages MUST NOT be negative.

This version imposes no maximum number of digits or fixed number of decimal places. Trailing zeros do not change the numerical value. The document's author supplies amounts at the intended precision. No rounding mode or currency-specific decimal-place restriction is implied.

## Currency

If any monetary amount is supplied, the estimate MUST supply `currency`. This includes estimate or work-item `cost` and `sellingPrice`, every recap cost category, and markup `amount`. A supplied zero is still a monetary amount. A markup containing only `percentage` and `basis` does not require currency. Currency MAY also be supplied before any monetary amounts are known.

`currency` MUST be a three-uppercase-letter ISO 4217 currency code, such as `"USD"`. All amounts in a document use that currency. Multiple currencies and exchange-rate calculations are outside this version.

The schema checks the three-letter form, not whether ISO 4217 assigns the code. Schema validation alone does not establish that a currency code is assigned.

## Units

Unit codes are case-sensitive. This version permits only these codes:

| Code | Meaning |
| --- | --- |
| `ea` | Each, a count of the work described. |
| `in` | Inch. |
| `ft` | Foot. |
| `yd` | Yard. |
| `in2` | Square inch. |
| `ft2` | Square foot. |
| `yd2` | Square yard. |
| `in3` | Cubic inch. |
| `ft3` | Cubic foot. |
| `yd3` | Cubic yard. |
| `mm` | Millimeter. |
| `cm` | Centimeter. |
| `m` | Meter. |
| `m2` | Square meter. |
| `m3` | Cubic meter. |
| `h` | Hour. |
| `d` | Day of 24 hours, not a labor shift or workday. |

Inch, foot, and yard mean the international units, with one inch equal to exactly 0.0254 meter. Area and volume codes use the corresponding squared and cubed units. An hour is 60 minutes. These definitions do not require an estimate to provide converted quantities.

Names such as `roll`, `box`, `square`, and `day shift` are not interchangeable with the codes above and are not defined as units in this version. A roof area expressed in roofing squares can instead be stated in square feet. Package definitions and custom units are deferred.

## Costs, selling prices, and totals

Cost is the amount expected to be incurred for the described work. Selling price is the amount intended to be charged. Either MAY appear without the other, at both estimate and work-item level. Neither implies a markup, margin, profit, or tax formula.

Each amount covers only the scope and conditions described by its estimate or item, including applicable notes, assumptions, and exclusions. There is no implicit rule that tax, overhead, or any other expense is included or excluded.

Estimate-wide `cost` and `sellingPrice` are supplied totals, not additional charges to add to work-item amounts.

Estimate totals, recap costs, markup values, and work-item amounts are independently supplied data. Their numerical agreement is not a condition of validity, even when all values are supplied. This version defines no summation, reconciliation, rounding, percentage application, or missing-value derivation rules.

If an estimate-wide amount is omitted, that total was not supplied, even if every item has an amount. If an item amount is omitted, that value was not supplied and is not established by subtraction from the total. These omissions MUST NOT be treated as zero.

For example, three item costs of `"33.33"` and a supplied estimate cost of `"100.00"` form a valid document. The DTO carries those values as stated; it does not require correcting them or explaining the difference. Checking calculation accuracy is outside conformance validation.

## Versioning and compatibility

`specVersion` identifies the exact set of field definitions and rules used by a document. A document declaring another version is not a `0.1.0` document, even if some fields look familiar.

This draft may change before release. Once released, a version's definitions MUST NOT be changed in ways that reinterpret existing documents. A change to fields, permitted values, or semantics requires a new version. This version makes no promise that a later version will be backward-compatible.

Unknown fields are not a substitute for a new version. Information outside this version cannot be claimed as standardized merely because it appears in a note.

## Conformance and validation

A conforming `0.1.0` document MUST satisfy:

1. The JSON and field rules in this document.
2. The [Draft 2020-12 JSON Schema](../schemas/estimate.schema.json).
3. The definitions of currency, units, and field meanings in this document.

Schema validation detects structural problems such as missing descriptions, malformed decimals, missing currency, a markup percentage without its basis, unsupported units, and unknown fields. It cannot establish whether a description accurately represents the work or whether a currency code is assigned. Conformance validation MUST NOT recalculate amounts or require totals to agree with item amounts.

The schema can be used with any validator supporting JSON Schema Draft 2020-12. This repository does not currently include a validator or test runner.

See the [examples](../examples/README.md) for complete documents covering each supported form.
