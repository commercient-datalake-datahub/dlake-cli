---
name: dlake-crmpro-zohocrm/erps/epicor-eagle
kind: erp-summary
description: >-
  Use it when standing up or reading an Epicor Eagle → Zoho CRM template set, when deciding which
  templates to import and activate, or when a run completes without pushing records and the answer
  is in the view or the configuration row. It extends dlake-crmpro, which covers operating CRMPro
  generally, and dlake-crmpro-zohocrm, the destination skill this page is a child of, which carries
  the Zoho CRM conventions that hold across every ERP.
---
# CRMPro → Zoho CRM — Epicor Eagle: what the shipped templates set up

This is a capability summary, not an operating guide. To receive the operational skill you must be a
registered, whitelisted Commercient customer. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/epicor-eagle` (or `list_skills`) against the
Commercient admin plane. Existing customers who need access or help: contact
support@commercient.com. New customers: contact sales@commercient.com to become a customer and be
whitelisted.

dlake-crmpro is the parent skill and the authority for everything general: the CRMPro tools, process
configuration and field list, the sync history, how source data is selected, and what a run that
finds nothing does. Read it first; this page does not repeat it. dlake-crmpro-zohocrm is the
destination skill this page is a child of, and the authority for the Zoho CRM conventions that hold
across every ERP: read it first, then come back here for what this source's own templates set. This
page grows as the catalogue does.

## 1. What the templates deliver

| Group | Business outcome | Objects | Source tables and views |
|---|---|---|---|
| **Epicor Eagle Terms** | The templates push Commercient Epicor Eagle terms code object to Zoho CRM. | Commercient Epicor Eagle terms code object | terms codes |
| **Epicor Eagle Salesperson** | The templates push Commercient Epicor Eagle salesperson object to Zoho CRM. | Commercient Epicor Eagle salesperson object | salespeople |
| **Account** | The templates push Accounts to Zoho CRM. | Accounts | customers, customer shipping addresses |
| **Epicor Eagle Customer** | The templates push Commercient Epicor Eagle customer object to Zoho CRM. | Commercient Epicor Eagle customer object | customers |
| **Epicor Eagle Store** | The templates push Commercient Epicor Eagle inventory store object to Zoho CRM. | Commercient Epicor Eagle inventory store object | stores |
| **Product** | The templates push Products to Zoho CRM. | Products | inventory records |
| **Epicor Eagle Inventory** | The templates push Commercient Epicor Eagle inventory object to Zoho CRM. | Commercient Epicor Eagle inventory object | inventory records |
| **Epicor Eagle Sales order header** | The templates push Commercient Epicor Eagle sales order header object to Zoho CRM. | Commercient Epicor Eagle sales order header object | point of sale order headers |
| **Epicor Eagle Sales order detail** | The templates push Commercient Epicor Eagle sales order detail object to Zoho CRM. | Commercient Epicor Eagle sales order detail object | point of sale order lines |
| **Epicor Eagle Invoice header** | The templates push Commercient Epicor Eagle invoice header object to Zoho CRM. | Commercient Epicor Eagle invoice header object | point of sale transaction headers |
| **Epicor Eagle Invoice detail** | The templates push Commercient Epicor Eagle invoice detail object to Zoho CRM. | Commercient Epicor Eagle invoice detail object | point of sale transaction lines |
| **Epicor Eagle open invoice sync output** | The templates push Commercient Epicor Eagle open invoice header object to Zoho CRM. | Commercient Epicor Eagle open invoice header object | AR documents |
| **Epicor Eagle Open invoice detail** | The templates push Commercient Epicor Eagle open invoice detail object to Zoho CRM. | Commercient Epicor Eagle open invoice detail object | archived AR documents |
| **Epicor Eagle Serial number** | The templates push Epicor Eagle serial number (custom object) to Zoho CRM. | Epicor Eagle serial number (custom object) | serial numbers |
| **Pricebook** | The templates push Zoho price books to Zoho CRM. | Zoho price books | price plan headers |
| **Product Pricebook** | The templates push Zoho product price book relation to Zoho CRM. | Zoho product price book relation | price plan details |
| **Sales orders** | The templates push Sales orders to Zoho CRM. | Sales orders | point of sale order lines, point of sale order headers, customers, customer shipping addresses, x |
| **Invoices** | The templates push Invoices to Zoho CRM. | Invoices | point of sale transaction lines, point of sale transaction headers, customers, customer shipping addresses, x |

## 2. The process rows the templates create

Each template's setup writes one process configuration row. These are the values it sets; a column
the inserts never set is not listed.

| Display name | Destination object | Matching key | Run sequence |
|---|---|---|---|
| Epicor Eagle Terms | Commercient Epicor Eagle terms code object | Commercient external key column | 1 |
| Epicor Eagle Salesperson | Commercient Epicor Eagle salesperson object | Commercient external key column | 2 |
| Account | Accounts | Commercient AR customer code (Zoho field) | 3 |
| Epicor Eagle Customer | Commercient Epicor Eagle customer object | Commercient external key column | 4 |
| Epicor Eagle Store | Commercient Epicor Eagle inventory store object | Commercient external key column | 5 |
| Product | Products | Commercient external key column | 6 |
| Epicor Eagle Inventory | Commercient Epicor Eagle inventory object | Commercient external key column | 7 |
| Epicor Eagle Sales order header | Commercient Epicor Eagle sales order header object | Commercient external key column | 8 |
| Epicor Eagle Sales order detail | Commercient Epicor Eagle sales order detail object | Commercient external key column | 9 |
| Epicor Eagle Invoice header | Commercient Epicor Eagle invoice header object | Commercient external key column | 10 |
| Epicor Eagle Invoice detail | Commercient Epicor Eagle invoice detail object | Commercient external key column | 11 |
| Epicor Eagle open invoice sync output | Commercient Epicor Eagle open invoice header object | Commercient external key column | 12 |
| Epicor Eagle Open invoice detail | Commercient Epicor Eagle open invoice detail object | Commercient external key column | 13 |
| Epicor Eagle Serial number | Epicor Eagle serial number (custom object) | Commercient external key column | 14 |
| Pricebook | Zoho price books | Commercient external key column | 15 |
| Product Pricebook | Zoho product price book relation | Commercient external key column | 16 |
| Sales orders | Sales orders | Commercient external key column | 17 |
| Invoices | Invoices | Commercient external key column | 18 |

Every one of these inserts the active setting as 0, so an imported process is inactive until an
operator activates it.

## 3. The views

"Change detection" names which of the three kinds dlake-crmpro defines the view's own filter makes
it; where a view's shape is not one of those three the column is left empty and the view's own
filter is the authority.

| View | Change detection | Source tables |
|---|---|---|
| terms feed | insert only | terms codes |
| salesperson feed | insert only | salespeople |
| account feed | insert + update | customers, customer shipping addresses |
| customer feed | insert only | customers |
| store feed | insert only | stores |
| product feed | insert only | inventory records |
| inventory feed | insert only | inventory records |
| sales order feed | insert only | point of sale order headers |
| sales order line feed | insert only | point of sale order lines |
| invoice feed (Commercient objects) | insert only | point of sale transaction headers |
| invoice line feed (Commercient objects) | insert only | point of sale transaction lines |
| open invoice feed | insert only | AR documents |
| open invoice line feed | insert only | archived AR documents |
| serial number feed | insert only | serial numbers |
| price book feed | insert only | price plan headers |
| product price book feed | insert only | price plan details |
| Zoho sales order feed | insert only | point of sale order lines, point of sale order headers, customers, customer shipping addresses, x |
| Zoho invoice feed | insert only | point of sale transaction lines, point of sale transaction headers, customers, customer shipping addresses, x |

## 4. Order of work

The templates set run sequence from 1 to 18. A run processes active rows in ascending run sequence,
which is the order the templates put them in:

- 1 — Epicor Eagle Terms
- 2 — Epicor Eagle Salesperson
- 3 — Account
- 4 — Epicor Eagle Customer
- 5 — Epicor Eagle Store
- 6 — Product
- 7 — Epicor Eagle Inventory
- 8 — Epicor Eagle Sales order header
- 9 — Epicor Eagle Sales order detail
- 10 — Epicor Eagle Invoice header
- 11 — Epicor Eagle Invoice detail
- 12 — Epicor Eagle open invoice sync output
- 13 — Epicor Eagle Open invoice detail
- 14 — Epicor Eagle Serial number
- 15 — Pricebook
- 16 — Product Pricebook
- 17 — Sales orders
- 18 — Invoices

These views read another process's sync output, which is what makes the order a dependency order:
the row appears in the view only once the process that writes that sync output has run, so a parent
flows on one run and its children on the next.

- terms feed reads terms sync output; no template in this set writes terms sync output
- salesperson feed reads salesperson sync output; no template in this set writes salesperson sync
  output
- account feed reads terms sync output, salesperson sync output, account sync output; no template in
  this set writes terms sync output, salesperson sync output, account sync output
- customer feed reads account sync output, terms sync output, customer sync output; no template in
  this set writes account sync output, terms sync output, customer sync output
- store feed reads store sync output; no template in this set writes store sync output
- product feed reads store sync output, product sync output; no template in this set writes store
  sync output, product sync output
- inventory feed reads store sync output, inventory sync output; no template in this set writes
  store sync output, inventory sync output
- sales order feed reads account sync output, customer sync output, sales order sync output; no
  template in this set writes account sync output, customer sync output, sales order sync output
- sales order line feed reads sales order sync output, product sync output, sales order line sync
  output; no template in this set writes sales order sync output, product sync output, sales order
  line sync output
- invoice feed (Commercient objects) reads account sync output, customer sync output, invoice sync
  output (Commercient objects); no template in this set writes account sync output, customer sync
  output, invoice sync output (Commercient objects)
- invoice line feed (Commercient objects) reads invoice sync output (Commercient objects), product
  sync output, invoice line sync output (Commercient objects); no template in this set writes
  invoice sync output (Commercient objects), product sync output, invoice line sync output
  (Commercient objects)
- open invoice feed reads account sync output, customer sync output, open invoice sync output; no
  template in this set writes account sync output, customer sync output, open invoice sync output
- open invoice line feed reads open invoice sync output, open invoice line sync output; no template
  in this set writes open invoice sync output, open invoice line sync output
- serial number feed reads serial number sync output; no template in this set writes serial number
  sync output
- price book feed reads price book sync output; no template in this set writes price book sync
  output
- product price book feed reads product sync output, price book sync output, product price book sync
  output; no template in this set writes product sync output, price book sync output, product price
  book sync output
- Zoho sales order feed reads product sync output, account sync output, Zoho sales order sync
  output; no template in this set writes product sync output, account sync output, Zoho sales order
  sync output
- Zoho invoice feed reads product sync output, account sync output, Zoho invoice sync output; no
  template in this set writes product sync output, account sync output, Zoho invoice sync output

## 5. Field mapping

Each template carries its intended mapping in field mapping.

| Template | Object | Mapped fields | First ERP → Zoho CRM pairs |
|---|---|---|---|
| Epicor Eagle Terms | Commercient Epicor Eagle terms code object | 25 | Terms code → Commercient external key (custom field), Description 1 → Name, Terms code → Commercient terms code, Terms due days → Commercient due days, Due date → Due date |
| Epicor Eagle Salesperson | Commercient Epicor Eagle salesperson object | 14 | Salesperson → Commercient external key (custom field), Salesperson name → Name, Salesperson → Salesperson, Salesperson name → Salesperson name, Territory → Territory |
| Account | Accounts | 17 | Customer, Job number → Commercient AR customer code (Zoho field), Name, Customer, Job number → Account name, Phone number → Phone, Address 1, Address 2 → Billing street (Zoho field), City → Billing city (Zoho field) |
| Epicor Eagle Customer | Commercient Epicor Eagle customer object | 169 | Customer, Job number → Commercient external key (custom field), the linked Salesforce record → Account, the linked Salesforce record → Terms, Customer → Customer, Job number → Job number |
| Epicor Eagle Store | Commercient Epicor Eagle inventory store object | 146 | Store → Commercient external key (custom field), Store name → Name, Additional charges → Commercient additional charges, Address line 1 → Commercient address line 1, Address line 2 → Commercient address line 2 |
| Product | Products | 3 | Stock keeping unit → Commercient external key (custom field), Description → Product name (Zoho field), Store → Epicor Eagle store (related record) |
| Epicor Eagle Inventory | Commercient Epicor Eagle inventory object | 173 | Stock keeping unit, Store → Commercient external key (custom field), Description, Store → Name, the linked store record → Commercient Epicor Eagle inventory store (related record), Alternate conversion factor → Commercient alternate conversion factor, Alternate decimal places → Commercient alternate decimal places |
| Epicor Eagle Sales order header | Commercient Epicor Eagle sales order header object | 85 | Document number (Epicor Eagle), Store → Commercient external key (custom field), Document number (Epicor Eagle), Store → Name, Customer → Account, Customer → Commercient Epicor Eagle customer (related record), Document number (Epicor Eagle) → Commercient order document number |
| Epicor Eagle Sales order detail | Commercient Epicor Eagle sales order detail object | 65 | Document number (Epicor Eagle), Store, Source line number → Commercient external key (custom field), Document number (Epicor Eagle), Store, Source line number → Name, the linked Salesforce record → Commercient Epicor Eagle sales order header (related record), the linked Salesforce record → Product, Document number (Epicor Eagle) → Document number |
| Epicor Eagle Invoice header | Commercient Epicor Eagle invoice header object | 85 | Document number (Epicor Eagle), Store → Commercient external key (custom field), Document number (Epicor Eagle), Store → Name, Customer → Account, Customer → Commercient Epicor Eagle customer (related record), Document number (Epicor Eagle) → Document number |
| Epicor Eagle Invoice detail | Commercient Epicor Eagle invoice detail object | 67 | Document number (Epicor Eagle), Store, Source line number → Commercient external key (custom field), Document number (Epicor Eagle), Store, Source line number → Name, the linked Salesforce record → Commercient Epicor Eagle invoice header (related record), the linked Salesforce record → Product, Document number (Epicor Eagle) → Document number |
| Epicor Eagle open invoice sync output | Commercient Epicor Eagle open invoice header object | 91 | Document number, Store → Commercient external key (custom field), Document number, Store → Name, Customer, Job number → Account, Customer, Job number → Commercient Epicor Eagle customer (related record), Store → Store |
| Epicor Eagle Open invoice detail | Commercient Epicor Eagle open invoice detail object | 66 | Archive time, Archive sequence → Commercient external key (custom field), Archive time, Archive sequence → Name, the linked Salesforce record → Commercient Epicor Eagle open invoice header (related record), Alternate tender 1 amount → Commercient alternate tender 1 amount, Alternate tender 1 type → Commercient alternate tender 1 type |
| Epicor Eagle Serial number | Epicor Eagle serial number (custom object) | 9 | Serial number,Item number → Commercient external key column, Serial number,Item number → Name, Serial number → Serial number (Zoho field), Item number → Item number, Store → Store |
| Pricebook | Zoho price books | 4 | Category plan → Commercient external key column, Category plan → Price book name (Zoho field), Plan description → Description |
| Product Pricebook | Zoho product price book relation | 4 | Category plan, Category type, Category, Core plan → Commercient external key column, the linked Salesforce record → Product lookup (Zoho field), the linked Salesforce record → Price book lookup (Zoho field), Category price → List price |
| Sales orders | Sales orders | 18 | Document number (Epicor Eagle), Store → Commercient external key (custom field), Customer, Job number → Account name, Document number (Epicor Eagle) → Name, Document number (Epicor Eagle) → Sales order number (Zoho field), Document date → ERP created date (custom field) |
| Invoices | Invoices | 19 | Document number (Epicor Eagle), Store → Commercient external key (custom field), Customer, Job number → Account name, Document number (Epicor Eagle) → Name, Document number (Epicor Eagle) → Invoice number (Zoho field), Document date → Invoice date |

## 6. Community templates

The catalogue carries 19 community templates for this pair. A community template is authored in a
tenant rather than shipped with the product, and it imports the same way as any other. Its own
names, notes, field mapping and SQL are tenant content, so what this section states is what the set
amounts to: how many templates there are, what they default to doing, which destination objects they
write and which groups they fall in. They are not part of the shipped set described above.

- Templates: 19
- Default operations: insert on 19, update on 19, delete on 19
- Marked as circular sync: 0
- Licence groups they span: 4
- Destination objects: Accounts, Commercient Epicor Eagle customer object, Commercient Epicor Eagle
  inventory object, Commercient Epicor Eagle invoice detail object, Commercient Epicor Eagle invoice
  header object, Commercient Epicor Eagle inventory store object, Commercient Epicor Eagle
  salesperson object, Commercient Epicor Eagle sales order detail object, Commercient Epicor Eagle
  sales order header object, Commercient Epicor Eagle terms code object, Contacts, Epicor Eagle
  serial number (custom object), Invoices, Zoho price books, Products, Sales orders and 3 custom
  objects
- Object display names: Account, Contacts, Epicor Eagle Customer, Epicor Eagle Inventory, Epicor
  Eagle Invoice detail, Epicor Eagle Invoice header, Epicor Eagle open invoice sync output, Epicor
  Eagle Open invoice detail, Epicor Eagle Sales order detail, Epicor Eagle Sales order header,
  Epicor Eagle Salesperson, Epicor Eagle Serial number and 7 more
- Template groups: Account, CRM Order and Line

## 7. Verifying

The detail of this section is part of the operational skill. Whitelisted customers fetch it with
`dlake admin get_skill dlake-crmpro-zohocrm/erps/epicor-eagle`.

## 8. Where this sits

dlake-crmpro is the general operating surface — the CRMPro tools, the setup and transaction tables,
field mapping, and the source view contract that applies to every destination. This page adds what
the shipped Epicor Eagle → Zoho CRM templates set up. dlake-crmpro-zohocrm is the destination skill
this page sits under: its own text is the authority for the Zoho CRM conventions that hold across
every ERP, and its ERP table lists this page alongside every sibling ERP page for this destination.
For the extract leg that fills the source data, see dlake-normalsync; for the on-premises agent that
runs it, dlake-syncagent; for the writeback leg, dlake-txdownloaderpro; for standing an integration
up, dlake-integration-setup.

## How to get started

- **Already a customer:** the operational skill tells you how to connect, choose what to sync and run it.
  Fetch it with `dlake admin get_skill dlake-crmpro-zohocrm/erps/epicor-eagle`, or ask support@commercient.com.
- **New to Commercient:** contact sales@commercient.com. Becoming a customer includes registration and
  whitelisting, after which the operational skills and tools are available to you.
