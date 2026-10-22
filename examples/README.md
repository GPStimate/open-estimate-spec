# Examples

These JSON documents conform to the [0.1.0 draft](../docs/specification.md). They use U.S. customary units and U.S. dollars for illustration. The specification also defines metric units and permits other ISO 4217 currencies.

| Document | Demonstrates |
| --- | --- |
| [Recap](recap.json) | All five estimate-level cost categories, totals, a percentage-and-amount markup, and an amount-only markup, without work items or resource lists. |
| [Percentage-only recap](recap-percentage-only.json) | A stated markup percentage and its basis, without a monetary amount or currency. |
| [Unfinished](unfinished.json) | The smallest document: version and scope description, without amounts. |
| [Cost-only](cost-only.json) | A lump-sum estimated cost, without a selling price. |
| [Price-only](price-only.json) | A lump-sum selling price, without disclosed costs. |
| [Combined lump sum](combined-lump-sum.json) | Cost and selling price, project context, assumptions, and exclusions, without a breakdown. |
| [Itemized](itemized.json) | Work quantities with explicit units and complete cost and selling-price breakdowns. |
| [Stated totals](stated-totals.json) | Supplied totals that differ from item sums. Arithmetic agreement is not required for validity. |
| [Incomplete itemized](incomplete-itemized.json) | A supplied estimate-wide cost with a partially supplied item cost breakdown. No missing cost is inferred. |
| [Credit and zero](credit-and-zero.json) | Negative monetary amounts for a credit and explicit zero amounts for an included inspection. |

The itemized example has a cost of USD 5,800 and selling price of USD 7,250. Its work-item amounts are already extended amounts. They are not multiplied by their quantities again.

The incomplete example supplies no selling price. Its missing item cost is not zero, and the document does not supply that value by subtraction from the estimate total.

Each example can be checked against the [JSON Schema](../schemas/estimate.schema.json) using a validator supporting JSON Schema Draft 2020-12.
