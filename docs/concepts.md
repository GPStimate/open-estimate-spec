# Purpose, scope, and core concepts

## Status

This is non-normative background for the [0.1.0 specification](specification.md). It describes a broader vocabulary and possible scope, including concepts not represented in the first version. The specification, not this document, defines the current fields and conformance rules.

## Purpose

Open Estimate Spec aims to give construction-estimating tools a common way to represent and exchange estimates.

Two tools exchanging an estimate need a shared understanding of what the work includes, how it is quantified, and what its monetary amounts mean. Matching field names is not enough. A quantity of 10 could mean 10 square feet of installed work, 10 packages to purchase, or 10 hours of labor. A monetary amount could be a unit cost, an extended cost, or a selling price.

The specification should make these meanings explicit without requiring knowledge of any particular application, database, or estimating workflow. It defines a data transfer object, not calculation logic. Quantities and amounts are supplied data, not values the specification requires a consumer to calculate or reconcile.

## Intended use

The specification is intended for exchanging construction estimates between estimating tools and other software that uses estimate information.

An estimate may contain a simple list of priced work or a detailed breakdown of the resources and calculations behind that work. The conceptual model should accommodate both without implying that missing detail is zero or that every estimate must use the same estimating method.

An estimate is not necessarily a bid or proposal. A bid or proposal can use estimate information while also including commercial terms that are outside the estimate itself.

## Proposed scope

The initial scope is the information needed to describe estimated construction work and interpret its quantities and amounts:

- Estimate identity, description, and project context.
- The work included, its organization, and stated exclusions or assumptions.
- Work quantities and their units.
- Resource requirements and their relationship to the work.
- Costs, selling prices, and adjustments, with their bases identified.
- References that explain or support the estimate.

These are areas to define, not a list of mandatory fields.

The initial scope does not prescribe:

- Application screens, database structures, or internal identifiers.
- How software edits, imports, exports, or recalculates estimates.
- A universal cost database, product catalog, or classification system.
- Drawing geometry, building models, or document-storage formats.
- Contract terms, invoicing, payment processing, or project scheduling.

An estimate may refer to information outside this scope. A supporting drawing, for example, does not require the specification to define a drawing format.

## Core concepts

The terms below describe concepts rather than proposed field names. Exact representation and required relationships remain open.

### Estimate

A description of anticipated construction work and its associated quantities and monetary amounts, prepared for a stated purpose.

The specification supports cost-only estimates, selling-price-only estimates, and estimates containing both. Cost and selling price are independently supplied amounts. An omitted amount means it was not supplied, not that it is zero or equal to the other amount. Each supplied monetary amount must identify whether it represents cost or selling price.

For example, the same roof-flashing work could be represented with an estimated cost of USD 4,000, a selling price of USD 5,000, or both. Supplying only one does not establish the other.

An estimate can describe its entire scope with a single lump-sum cost, selling price, or both. Individual work items, measured quantities, and resource breakdowns are not required for this form of estimate.

### Recap

An estimate-wide summary of costs and markups. The first version defines material, labor, subcontract, equipment, and other cost fields, plus labeled markup amounts and percentages with stated bases. Estimate-wide cost and selling price remain separate supplied totals.

The recap does not require a material list, labor breakdown, or work items. Detail-level material and labor costs and selling prices, unit rates, resource lists, and labor hours are possible later additions, not prerequisites for recap exchange.

### Project context

Information identifying the construction project or work being estimated, such as its name, location, and description.

The project and the estimate are distinct. Several estimates may concern the same project.

### Scope of work

A description of the work included in an estimate or a portion of it. Assumptions, exclusions, and qualifications help define its boundaries.

Scope explains what a quantity or price covers. A description such as "install insulation" alone may not establish whether supply, removal, disposal, or surface preparation is included.

### Work item

An identifiable portion of estimated work, such as installing roof insulation or placing a concrete slab.

A work item may have a description, a quantity, costs, selling prices, and a resource breakdown. Some work is estimated as a lump sum rather than a measured quantity.

### Group

An organizational collection of work items, such as a trade, building area, or phase of work.

Grouping and classification are different. An item can belong to a project-specific group while also carrying a classification code. The specification has not yet chosen a hierarchy or membership model.

### Quantity and unit

A quantity expresses how much of something is involved. Its unit and purpose establish its meaning.

Work quantities, resource requirements, purchase quantities, and pricing quantities may differ. These quantities should not be treated as interchangeable simply because they concern the same work item.

A unit can express a physical measure, a count, or a defined package. A package name such as "roll" does not establish its contents without additional information.

### Resource

Something required to perform the work, such as material, labor, equipment, or a subcontracted service.

A resource description identifies what is needed. Its use in a work item describes how much is required and on what basis. The same resource can be used in several work items.

Not every estimate identifies resources separately. A subcontract quotation, for example, may provide a single amount for an entire work item.

### Resource requirement

The relationship between work and a resource needed to perform it.

A requirement may be expressed as a fixed amount, an amount per unit of work, a production rate, or another stated basis. Coverage, waste allowances, and package sizes can affect the resulting quantities, but describe different things.

### Cost

An amount expected to be incurred for a resource, work item, or other part of the estimate.

A unit cost needs a quantity basis. A total cost needs an identified scope. Monetary amounts also need a currency.

### Selling price

An amount intended to be charged for the estimated work.

Cost and selling price are distinct even when their values happen to match. A selling price may be stated directly or derived from costs and adjustments.

### Adjustment

An amount or rule that changes a stated cost or selling price, such as an allowance, tax, discount, or addition for overhead and profit.

An adjustment needs an identified purpose and basis. A percentage without that basis is ambiguous. Markup and margin also have different meanings: markup expresses profit as a percentage of cost, while margin expresses it as a percentage of selling price.

### Total

A monetary amount covering an identified set of items and adjustments.

A total needs to distinguish what it includes. A subtotal before tax and a final selling price are not equivalent amounts. Where a breakdown and a total are both supplied, their relationship needs to be clear.

### Classification and reference

A classification associates estimate information with a named coding system or category. A code needs its system identified to have a shared meaning.

A reference connects estimate information to supporting material, such as a specification, drawing, quotation, or product document. References provide context without requiring the referenced content to be embedded in the estimate.

## Worked example

Consider an estimate to supply and install roof membrane over 1,000 square feet of roof. Removal of the existing roof is excluded.

The membrane is supplied in rolls containing 250 square feet each. For this example, that is the usable coverage before a separate 10% waste allowance. Each roll costs USD 200. Labor is estimated at 0.02 hours per square foot of installed roof, at USD 40 per hour.

| Concept | Example value |
| --- | --- |
| Work item | Supply and install roof membrane |
| Work quantity | 1,000 square feet |
| Material requirement before waste | 1,000 square feet |
| Material requirement including waste | 1,100 square feet |
| Package contents | 250 square feet per roll |
| Purchase quantity | 5 whole rolls |
| Purchased material quantity | 1,250 square feet |
| Material unit cost | USD 200 per roll |
| Material cost | USD 1,000 |
| Labor requirement | 20 hours |
| Labor unit cost | USD 40 per hour |
| Labor cost | USD 800 |
| Total estimated cost | USD 1,800 |
| Selling price | USD 2,250 |

The purchase quantity reflects whole-roll rounding. The difference between 1,100 square feet required and 1,250 square feet purchased is not the waste percentage.

The selling price in this example uses a 25% markup on cost, equivalent to a 20% margin on selling price. No other costs, adjustments, or taxes are included.

This example illustrates meaning, not a required calculation method. A shared representation must distinguish the work quantity, material requirement, purchase quantity, pricing basis, cost, and selling price even if two tools calculate them differently.

## Questions still open

- How should later versions build on the first version's minimum of a version identifier and scope description?
- How should groups, nested work, and shared resources relate?
- How should alternatives, revisions, and optional work be represented?
- How should units, package definitions, and conversions be identified?
- Is descriptive data about calculation bases and rounding needed beyond the first version's stated decimal amounts, without requiring execution or reconciliation?
- Are explicit unknown or not-applicable states needed beyond the first version's distinction between omitted values and supplied zero values?
- How should external classifications, references, and extensions be identified?

These questions concern later versions. The first version deliberately leaves these broader relationships and calculation models undefined.
